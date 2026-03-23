---
name: springboot-security
description: Spring Security best practices for authn/authz, validation, CSRF, secrets, headers, rate limiting, and dependency security in Java Spring Boot services.
description zh-CN: Java Spring Boot服务中Spring Security的最佳实践，涵盖认证/授权、验证、CSRF、密钥、头、限流和依赖安全。
origin: ECC
---

# Spring Boot Security Review

## English

Use when adding auth, handling input, creating endpoints, or dealing with secrets.

## 中文

在添加认证、处理输入、创建端点或处理密钥时使用。

## When to Activate

## English

- Adding authentication (JWT, OAuth2, session-based)
- Implementing authorization (@PreAuthorize, role-based access)
- Validating user input (Bean Validation, custom validators)
- Configuring CORS, CSRF, or security headers
- Managing secrets (Vault, environment variables)
- Adding rate limiting or brute-force protection
- Scanning dependencies for CVEs

## 中文

- 添加认证（JWT、OAuth2、会话式）
- 实现授权（@PreAuthorize、基于角色的访问）
- 验证用户输入（Bean验证、自定义验证器）
- 配置CORS、CSRF或安全头
- 管理密钥（Vault、环境变量）
- 添加限流或暴力破解保护
- 扫描依赖中的CVE

## Authentication / 认证

- Prefer stateless JWT or opaque tokens with revocation list
- 优先使用无状态JWT或带撤销列表的不透明令牌
- Use `httpOnly`, `Secure`, `SameSite=Strict` cookies for sessions
- 为会话使用`httpOnly`、`Secure`、`SameSite=Strict` cookie
- Validate tokens with `OncePerRequestFilter` or resource server
- 使用`OncePerRequestFilter`或资源服务器验证令牌

```java
@Component
public class JwtAuthFilter extends OncePerRequestFilter {
  private final JwtService jwtService;

  public JwtAuthFilter(JwtService jwtService) {
    this.jwtService = jwtService;
  }

  @Override
  protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response,
      FilterChain chain) throws ServletException, IOException {
    String header = request.getHeader(HttpHeaders.AUTHORIZATION);
    if (header != null && header.startsWith("Bearer ")) {
      String token = header.substring(7);
      Authentication auth = jwtService.authenticate(token);
      SecurityContextHolder.getContext().setAuthentication(auth);
    }
    chain.doFilter(request, response);
  }
}
```

## Authorization / 授权

- Enable method security: `@EnableMethodSecurity`
- 启用方法安全：`@EnableMethodSecurity`
- Use `@PreAuthorize("hasRole('ADMIN')")` or `@PreAuthorize("@authz.canEdit(#id)")`
- 使用`@PreAuthorize("hasRole('ADMIN')")`或`@PreAuthorize("@authz.canEdit(#id)")`
- Deny by default; expose only required scopes
- 默认拒绝；仅暴露所需的范围

- 启用方法安全：`@EnableMethodSecurity`
- 使用`@PreAuthorize("hasRole('ADMIN')")`或`@PreAuthorize("@authz.canEdit(#id)")`
- 默认拒绝；仅暴露所需的范围

```java
@RestController
@RequestMapping("/api/admin")
public class AdminController {

  @PreAuthorize("hasRole('ADMIN')")
  @GetMapping("/users")
  public List<UserDto> listUsers() {
    return userService.findAll();
  }

  @PreAuthorize("@authz.isOwner(#id, authentication)")
  @DeleteMapping("/users/{id}")
  public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
    userService.delete(id);
    return ResponseEntity.noContent().build();
  }
}
```

## Input Validation / 输入验证

- Use Bean Validation with `@Valid` on controllers
- 在controllers上使用Bean验证和`@Valid`
- Apply constraints on DTOs: `@NotBlank`, `@Email`, `@Size`, custom validators
- 在DTO上应用约束：`@NotBlank`、`@Email`、`@Size`、自定义验证器
- Sanitize any HTML with a whitelist before rendering
- 渲染前使用白名单清理任何HTML

- 在controllers上使用Bean验证和`@Valid`
- 在DTO上应用约束：`@NotBlank`、`@Email`、`@Size`、自定义验证器
- 渲染前使用白名单清理任何HTML

```java
// BAD: No validation
// 差：无验证
@PostMapping("/users")
public User createUser(@RequestBody UserDto dto) {
  return userService.create(dto);
}

// GOOD: Validated DTO
// 好：验证过的DTO
public record CreateUserDto(
    @NotBlank @Size(max = 100) String name,
    @NotBlank @Email String email,
    @NotNull @Min(0) @Max(150) Integer age
) {}

@PostMapping("/users")
public ResponseEntity<UserDto> createUser(@Valid @RequestBody CreateUserDto dto) {
  return ResponseEntity.status(HttpStatus.CREATED)
      .body(userService.create(dto));
}
```

## SQL Injection Prevention / SQL注入防护

- Use Spring Data repositories or parameterized queries
- 使用Spring Data repositories或参数化查询
- For native queries, use `:param` bindings; never concatenate strings
- 对于原生查询，使用`:param`绑定；永远不要字符串拼接

- 使用Spring Data repositories或参数化查询
- 对于原生查询，使用`:param`绑定；永远不要字符串拼接

```java
// BAD: String concatenation in native query
// 差：原生查询中的字符串拼接
@Query(value = "SELECT * FROM users WHERE name = '" + name + "'", nativeQuery = true)

// GOOD: Parameterized native query
// 好：参数化原生查询
@Query(value = "SELECT * FROM users WHERE name = :name", nativeQuery = true)
List<User> findByName(@Param("name") String name);

// GOOD: Spring Data derived query (auto-parameterized)
// 好：Spring Data派生的查询（自动参数化）
List<User> findByEmailAndActiveTrue(String email);
```

## Password Encoding / 密码编码

- Always hash passwords with BCrypt or Argon2 — never store plaintext
- 始终使用BCrypt或Argon2哈希密码——永远不要存储明文
- Use `PasswordEncoder` bean, not manual hashing
- 使用`PasswordEncoder` bean，而不是手动哈希

- 始终使用BCrypt或Argon2哈希密码——永远不要存储明文
- 使用`PasswordEncoder` bean，而不是手动哈希

```java
@Bean
public PasswordEncoder passwordEncoder() {
  return new BCryptPasswordEncoder(12); // cost factor 12 成本因子12
}

// In service
// 在服务中
public User register(CreateUserDto dto) {
  String hashedPassword = passwordEncoder.encode(dto.password());
  return userRepository.save(new User(dto.email(), hashedPassword));
}
```

## CSRF Protection / CSRF保护

- For browser session apps, keep CSRF enabled; include token in forms/headers
- 对于浏览器会话应用，保持CSRF启用；在表单/头中包含令牌
- For pure APIs with Bearer tokens, disable CSRF and rely on stateless auth
- 对于使用Bearer令牌的纯API，禁用CSRF并依赖无状态认证

- 对于浏览器会话应用，保持CSRF启用；在表单/头中包含令牌
- 对于使用Bearer令牌的纯API，禁用CSRF并依赖无状态认证

```java
http
  .csrf(csrf -> csrf.disable())
  .sessionManagement(sm -> sm.sessionCreationPolicy(SessionCreationPolicy.STATELESS));
```

## Secrets Management / 密钥管理

- No secrets in source; load from env or vault
- 源代码中不放密钥；从环境或vault加载
- Keep `application.yml` free of credentials; use placeholders
- 保持`application.yml`无凭据；使用占位符
- Rotate tokens and DB credentials regularly
- 定期轮换令牌和数据库凭据

- 源代码中不放密钥；从环境或vault加载
- 保持`application.yml`无凭据；使用占位符
- 定期轮换令牌和数据库凭据

```yaml
# BAD: Hardcoded in application.yml
# 差：application.yml中硬编码
spring:
  datasource:
    password: mySecretPassword123

# GOOD: Environment variable placeholder
# 好：环境变量占位符
spring:
  datasource:
    password: ${DB_PASSWORD}

# GOOD: Spring Cloud Vault integration
# 好：Spring Cloud Vault集成
spring:
  cloud:
    vault:
      uri: https://vault.example.com
      token: ${VAULT_TOKEN}
```

## Security Headers / 安全头

```java
http
  .headers(headers -> headers
    .contentSecurityPolicy(csp -> csp
      .policyDirectives("default-src 'self'"))
    .frameOptions(HeadersConfigurer.FrameOptionsConfig::sameOrigin)
    .xssProtection(Customizer.withDefaults())
    .referrerPolicy(rp -> rp.policy(ReferrerPolicyHeaderWriter.ReferrerPolicy.NO_REFERRER)));
```

## CORS Configuration / CORS配置

- Configure CORS at the security filter level, not per-controller
- 在安全过滤器级别配置CORS，而不是每个controller
- Restrict allowed origins — never use `*` in production
- 限制允许的来源——生产环境永远不要使用`*`

- 在安全过滤器级别配置CORS，而不是每个controller
- 限制允许的来源——生产环境永远不要使用`*`

```java
@Bean
public CorsConfigurationSource corsConfigurationSource() {
  CorsConfiguration config = new CorsConfiguration();
  config.setAllowedOrigins(List.of("https://app.example.com"));
  config.setAllowedMethods(List.of("GET", "POST", "PUT", "DELETE"));
  config.setAllowedHeaders(List.of("Authorization", "Content-Type"));
  config.setAllowCredentials(true);
  config.setMaxAge(3600L);

  UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
  source.registerCorsConfiguration("/api/**", config);
  return source;
}

// In SecurityFilterChain:
// 在SecurityFilterChain中：
http.cors(cors -> cors.configurationSource(corsConfigurationSource()));
```

## Rate Limiting / 限流

- Apply Bucket4j or gateway-level limits on expensive endpoints
- 在昂贵端点上应用Bucket4j或网关级限制
- Log and alert on bursts; return 429 with retry hints
- 突发时记录和警报；返回429并带重试提示

- 在昂贵端点上应用Bucket4j或网关级限制
- 突发时记录和警报；返回429并带重试提示

```java
// Using Bucket4j for per-endpoint rate limiting
// 使用Bucket4j进行每端点限流
@Component
public class RateLimitFilter extends OncePerRequestFilter {
  private final Map<String, Bucket> buckets = new ConcurrentHashMap<>();

  private Bucket createBucket() {
    return Bucket.builder()
        .addLimit(Bandwidth.classic(100, Refill.intervally(100, Duration.ofMinutes(1))))
        .build();
  }

  @Override
  protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response,
      FilterChain chain) throws ServletException, IOException {
    String clientIp = request.getRemoteAddr();
    Bucket bucket = buckets.computeIfAbsent(clientIp, k -> createBucket());

    if (bucket.tryConsume(1)) {
      chain.doFilter(request, response);
    } else {
      response.setStatus(HttpStatus.TOO_MANY_REQUESTS.value());
      response.getWriter().write("{\"error\": \"Rate limit exceeded\"}");
    }
  }
}
```

## Dependency Security / 依赖安全

- Run OWASP Dependency Check / Snyk in CI
- 在CI中运行OWASP Dependency Check / Snyk
- Keep Spring Boot and Spring Security on supported versions
- 保持Spring Boot和Spring Security在支持的版本上
- Fail builds on known CVEs
- 已知CVE时使构建失败

- 在CI中运行OWASP Dependency Check / Snyk
- 保持Spring Boot和Spring Security在支持的版本上
- 已知CVE时使构建失败

## Logging and PII / 日志和PII

- Never log secrets, tokens, passwords, or full PAN data
- 永远不要记录密钥、令牌、密码或完整PAN数据
- Redact sensitive fields; use structured JSON logging
- 脱敏敏感字段；使用结构化JSON日志

- 永远不要记录密钥、令牌、密码或完整PAN数据
- 脱敏敏感字段；使用结构化JSON日志

## File Uploads / 文件上传

- Validate size, content type, and extension
- 验证大小、内容类型和扩展名
- Store outside web root; scan if required
- 存储在web根目录外；如需要则扫描

- 验证大小、内容类型和扩展名
- 存储在web根目录外；如需要则扫描

## Checklist Before Release / 发布前检查清单

- [ ] Auth tokens validated and expired correctly
- [ ] 认证令牌正确验证和过期
- [ ] Authorization guards on every sensitive path
- [ ] 每个敏感路径都有授权保护
- [ ] All inputs validated and sanitized
- [ ] 所有输入都经过验证和清理
- [ ] No string-concatenated SQL
- [ ] 无字符串拼接SQL
- [ ] CSRF posture correct for app type
- [ ] CSRF态势符合应用类型
- [ ] Secrets externalized; none committed
- [ ] 密钥外部化；无提交
- [ ] Security headers configured
- [ ] 安全头已配置
- [ ] Rate limiting on APIs
- [ ] API限流
- [ ] Dependencies scanned and up to date
- [ ] 依赖扫描并最新
- [ ] Logs free of sensitive data
- [ ] 日志无敏感数据

- [ ] 认证令牌正确验证和过期
- [ ] 每个敏感路径都有授权保护
- [ ] 所有输入都经过验证和清理
- [ ] 无字符串拼接SQL
- [ ] CSRF态势符合应用类型
- [ ] 密钥外部化；无提交
- [ ] 安全头已配置
- [ ] API限流
- [ ] 依赖扫描并最新
- [ ] 日志无敏感数据

**Remember**: Deny by default, validate inputs, least privilege, and secure-by-configuration first.

**记住**：默认拒绝，验证输入，最小权限，配置优先安全。
