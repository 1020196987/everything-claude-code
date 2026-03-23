---
name: springboot-patterns
description: Spring Boot architecture patterns, REST API design, layered services, data access, caching, async processing, and logging. Use for Java Spring Boot backend work.
description zh-CN: Spring Boot架构模式、REST API设计、分层服务、数据访问、缓存、异步处理和日志。用于Java Spring Boot后端工作。
origin: ECC
---

# Spring Boot Development Patterns

## English

Spring Boot architecture and API patterns for scalable, production-grade services.

## 中文

用于可扩展、生产级服务的Spring Boot架构和API模式。

## When to Activate

## English

- Building REST APIs with Spring MVC or WebFlux
- Structuring controller → service → repository layers
- Configuring Spring Data JPA, caching, or async processing
- Adding validation, exception handling, or pagination
- Setting up profiles for dev/staging/production environments
- Implementing event-driven patterns with Spring Events or Kafka

## 中文

- 使用Spring MVC或WebFlux构建REST API
- 构建controller → service → repository层
- 配置Spring Data JPA、缓存或异步处理
- 添加验证、异常处理或分页
- 为开发/预发布/生产环境设置profiles
- 使用Spring Events或Kafka实现事件驱动模式

## REST API Structure / REST API结构

```java
@RestController
@RequestMapping("/api/markets")
@Validated
class MarketController {
  private final MarketService marketService;

  MarketController(MarketService marketService) {
    this.marketService = marketService;
  }

  @GetMapping
  ResponseEntity<Page<MarketResponse>> list(
      @RequestParam(defaultValue = "0") int page,
      @RequestParam(defaultValue = "20") int size) {
    Page<Market> markets = marketService.list(PageRequest.of(page, size));
    return ResponseEntity.ok(markets.map(MarketResponse::from));
  }

  @PostMapping
  ResponseEntity<MarketResponse> create(@Valid @RequestBody CreateMarketRequest request) {
    Market market = marketService.create(request);
    return ResponseEntity.status(HttpStatus.CREATED).body(MarketResponse.from(market));
  }
}
```

## Repository Pattern (Spring Data JPA) / Repository模式（Spring Data JPA）

```java
public interface MarketRepository extends JpaRepository<MarketEntity, Long> {
  @Query("select m from MarketEntity m where m.status = :status order by m.volume desc")
  List<MarketEntity> findActive(@Param("status") MarketStatus status, Pageable pageable);
}
```

## Service Layer with Transactions / 带事务的服务层

```java
@Service
public class MarketService {
  private final MarketRepository repo;

  public MarketService(MarketRepository repo) {
    this.repo = repo;
  }

  @Transactional
  public Market create(CreateMarketRequest request) {
    MarketEntity entity = MarketEntity.from(request);
    MarketEntity saved = repo.save(entity);
    return Market.from(saved);
  }
}
```

## DTOs and Validation / DTO和验证

```java
public record CreateMarketRequest(
    @NotBlank @Size(max = 200) String name,
    @NotBlank @Size(max = 2000) String description,
    @NotNull @FutureOrPresent Instant endDate,
    @NotEmpty List<@NotBlank String> categories) {}

public record MarketResponse(Long id, String name, MarketStatus status) {
  static MarketResponse from(Market market) {
    return new MarketResponse(market.id(), market.name(), market.status());
  }
}
```

## Exception Handling / 异常处理

```java
@ControllerAdvice
class GlobalExceptionHandler {
  @ExceptionHandler(MethodArgumentNotValidException.class)
  ResponseEntity<ApiError> handleValidation(MethodArgumentNotValidException ex) {
    String message = ex.getBindingResult().getFieldErrors().stream()
        .map(e -> e.getField() + ": " + e.getDefaultMessage())
        .collect(Collectors.joining(", "));
    return ResponseEntity.badRequest().body(ApiError.validation(message));
  }

  @ExceptionHandler(AccessDeniedException.class)
  ResponseEntity<ApiError> handleAccessDenied() {
    return ResponseEntity.status(HttpStatus.FORBIDDEN).body(ApiError.of("Forbidden"));
  }

  @ExceptionHandler(Exception.class)
  ResponseEntity<ApiError> handleGeneric(Exception ex) {
    // Log unexpected errors with stack traces
    // 记录意外错误的堆栈跟踪
    return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
        .body(ApiError.of("Internal server error"));
  }
}
```

## Caching / 缓存

Requires `@EnableCaching` on a configuration class.
需要在配置类上启用`@EnableCaching`。

需要在配置类上启用`@EnableCaching`。

```java
@Service
public class MarketCacheService {
  private final MarketRepository repo;

  public MarketCacheService(MarketRepository repo) {
    this.repo = repo;
  }

  @Cacheable(value = "market", key = "#id")
  public Market getById(Long id) {
    return repo.findById(id)
        .map(Market::from)
        .orElseThrow(() -> new EntityNotFoundException("Market not found"));
  }

  @CacheEvict(value = "market", key = "#id")
  public void evict(Long id) {}
}
```

## Async Processing / 异步处理

Requires `@EnableAsync` on a configuration class.
需要在配置类上启用`@EnableAsync`。

需要在配置类上启用`@EnableAsync`。

```java
@Service
public class NotificationService {
  @Async
  public CompletableFuture<Void> sendAsync(Notification notification) {
    // send email/SMS
    // 发送邮件/短信
    return CompletableFuture.completedFuture(null);
  }
}
```

## Logging (SLF4J) / 日志（SLF4J）

```java
@Service
public class ReportService {
  private static final Logger log = LoggerFactory.getLogger(ReportService.class);

  public Report generate(Long marketId) {
    log.info("generate_report marketId={}", marketId);
    try {
      // logic
      // 逻辑
    } catch (Exception ex) {
      log.error("generate_report_failed marketId={}", marketId, ex);
      throw ex;
    }
    return new Report();
  }
}
```

## Middleware / Filters / 中间件/过滤器

```java
@Component
public class RequestLoggingFilter extends OncePerRequestFilter {
  private static final Logger log = LoggerFactory.getLogger(RequestLoggingFilter.class);

  @Override
  protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response,
      FilterChain filterChain) throws ServletException, IOException {
    long start = System.currentTimeMillis();
    try {
      filterChain.doFilter(request, response);
    } finally {
      long duration = System.currentTimeMillis() - start;
      log.info("req method={} uri={} status={} durationMs={}",
          request.getMethod(), request.getRequestURI(), response.getStatus(), duration);
    }
  }
}
```

## Pagination and Sorting / 分页和排序

```java
PageRequest page = PageRequest.of(pageNumber, pageSize, Sort.by("createdAt").descending());
Page<Market> results = marketService.list(page);
```

## Error-Resilient External Calls / 容错外部调用

```java
public <T> T withRetry(Supplier<T> supplier, int maxRetries) {
  int attempts = 0;
  while (true) {
    try {
      return supplier.get();
    } catch (Exception ex) {
      attempts++;
      if (attempts >= maxRetries) {
        throw ex;
      }
      try {
        Thread.sleep((long) Math.pow(2, attempts) * 100L);
      } catch (InterruptedException ie) {
        Thread.currentThread().interrupt();
        throw ex;
      }
    }
  }
}
```

## Rate Limiting (Filter + Bucket4j) / 限流（Filter + Bucket4j）

**Security Note**: The `X-Forwarded-For` header is untrusted by default because clients can spoof it.
**安全注意**：`X-Forwarded-For`头默认不可信，因为客户端可以伪造它。
Only use forwarded headers when:
仅在以下情况下使用转发头：
1. Your app is behind a trusted reverse proxy (nginx, AWS ALB, etc.)
1. 您的应用位于可信的反向代理（nginx、AWS ALB等）后面
2. You have registered `ForwardedHeaderFilter` as a bean
2. 您已将`ForwardedHeaderFilter`注册为bean
3. You have configured `server.forward-headers-strategy=NATIVE` or `FRAMEWORK` in application properties
3. 您已在application properties中配置了`server.forward-headers-strategy=NATIVE`或`FRAMEWORK`
4. Your proxy is configured to overwrite (not append to) the `X-Forwarded-For` header
4. 您的代理配置为覆盖（而不是追加）`X-Forwarded-For`头

When `ForwardedHeaderFilter` is properly configured, `request.getRemoteAddr()` will automatically
return the correct client IP from the forwarded headers. Without this configuration, use
`request.getRemoteAddr()` directly—it returns the immediate connection IP, which is the only
trustworthy value.
当`ForwardedHeaderFilter`正确配置时，`request.getRemoteAddr()`将自动从转发头返回正确的客户端IP。
如果没有此配置，请直接使用`request.getRemoteAddr()`——它返回直接连接IP，这是唯一可信的值。

```java
@Component
public class RateLimitFilter extends OncePerRequestFilter {
  private final Map<String, Bucket> buckets = new ConcurrentHashMap<>();

  /*
   * SECURITY: This filter uses request.getRemoteAddr() to identify clients for rate limiting.
   * 安全：此过滤器使用request.getRemoteAddr()来识别限流的客户端。
   *
   * If your application is behind a reverse proxy (nginx, AWS ALB, etc.), you MUST configure
   * Spring to handle forwarded headers properly for accurate client IP detection:
   * 如果您的应用位于反向代理（nginx、AWS ALB等）后面，您必须配置Spring正确处理转发头以准确定位客户端IP：
   *
   * 1. Set server.forward-headers-strategy=NATIVE (for cloud platforms) or FRAMEWORK in
   *    application.properties/yaml
   * 2. If using FRAMEWORK strategy, register ForwardedHeaderFilter:
   * 如果使用FRAMEWORK策略，注册ForwardedHeaderFilter：
   *
   *    @Bean
   *    ForwardedHeaderFilter forwardedHeaderFilter() {
   *        return new ForwardedHeaderFilter();
   *    }
   *
   * 3. Ensure your proxy overwrites (not appends) the X-Forwarded-For header to prevent spoofing
   * 确保您的代理覆盖（而不是追加）X-Forwarded-For头以防止伪造
   * 4. Configure server.tomcat.remoteip.trusted-proxies or equivalent for your container
   * 为您的容器配置server.tomcat.remoteip.trusted-proxies或等效配置
   *
   * Without this configuration, request.getRemoteAddr() returns the proxy IP, not the client IP.
   * 没有此配置，request.getRemoteAddr()返回代理IP，而不是客户端IP。
   * Do NOT read X-Forwarded-For directly—it is trivially spoofable without trusted proxy handling.
   * 不要直接读取X-Forwarded-For——没有可信代理处理，它很容易被伪造。
   */
  @Override
  protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response,
      FilterChain filterChain) throws ServletException, IOException {
    // Use getRemoteAddr() which returns the correct client IP when ForwardedHeaderFilter
    // is configured, or the direct connection IP otherwise. Never trust X-Forwarded-For
    // headers directly without proper proxy configuration.
    // 使用getRemoteAddr()，当ForwardedHeaderFilter配置正确时返回正确的客户端IP，
    // 否则返回直接连接IP。在没有适当代理配置的情况下，永远不要直接信任X-Forwarded-For头。
    String clientIp = request.getRemoteAddr();

    Bucket bucket = buckets.computeIfAbsent(clientIp,
        k -> Bucket.builder()
            .addLimit(Bandwidth.classic(100, Refill.greedy(100, Duration.ofMinutes(1))))
            .build());

    if (bucket.tryConsume(1)) {
      filterChain.doFilter(request, response);
    } else {
      response.setStatus(HttpStatus.TOO_MANY_REQUESTS.value());
    }
  }
}
```

## Background Jobs / 后台作业

Use Spring's `@Scheduled` or integrate with queues (e.g., Kafka, SQS, RabbitMQ). Keep handlers idempotent and observable.
使用Spring的`@Scheduled`或与队列集成（例如Kafka、SQS、RabbitMQ）。保持处理器幂等和可观察。

使用Spring的`@Scheduled`或与队列集成（例如Kafka、SQS、RabbitMQ）。保持处理器幂等和可观察。

## Observability / 可观测性

- Structured logging (JSON) via Logback encoder
- Metrics: Micrometer + Prometheus/OTel
- Tracing: Micrometer Tracing with OpenTelemetry or Brave backend
- 通过Logback编码器的结构化日志（JSON）
- 指标：Micrometer + Prometheus/OTel
- 追踪：Micrometer Tracing配合OpenTelemetry或Brave后端

- 通过Logback编码器的结构化日志（JSON）
- 指标：Micrometer + Prometheus/OTel
- 追踪：Micrometer Tracing配合OpenTelemetry或Brave后端

## Production Defaults / 生产默认值

- Prefer constructor injection, avoid field injection
- 优先使用构造函数注入，避免字段注入
- Enable `spring.mvc.problemdetails.enabled=true` for RFC 7807 errors (Spring Boot 3+)
- 为RFC 7807错误启用`spring.mvc.problemdetails.enabled=true`（Spring Boot 3+）
- Configure HikariCP pool sizes for workload, set timeouts
- 为工作负载配置HikariCP池大小，设置超时
- Use `@Transactional(readOnly = true)` for queries
- 对查询使用`@Transactional(readOnly = true)`
- Enforce null-safety via `@NonNull` and `Optional` where appropriate
- 在适当的地方通过`@NonNull`和`Optional`强制空安全

- 优先使用构造函数注入，避免字段注入
- 为RFC 7807错误启用`spring.mvc.problemdetails.enabled=true`（Spring Boot 3+）
- 为工作负载配置HikariCP池大小，设置超时
- 对查询使用`@Transactional(readOnly = true)`
- 在适当的地方通过`@NonNull`和`Optional`强制空安全

**Remember**: Keep controllers thin, services focused, repositories simple, and errors handled centrally. Optimize for maintainability and testability.

**记住**：保持controllers瘦、services专注、repositories简单，错误集中处理。优化以提高可维护性和可测试性。
