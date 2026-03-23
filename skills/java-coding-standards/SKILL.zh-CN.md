---
name: java-coding-standards
description: "Java coding standards for Spring Boot services: naming, immutability, Optional usage, streams, exceptions, generics, and project layout."
description zh-CN: "Java Spring Boot 服务编码规范：命名、不可变性、Optional 使用、流处理、异常处理、泛型和项目布局。"
origin: ECC
---

# Java Coding Standards
# Java 编码规范

Standards for readable, maintainable Java (17+) code in Spring Boot services.
Spring Boot 服务中可读、可维护 Java（17+）代码的标准。

## When to Activate
## 何时激活

- Writing or reviewing Java code in Spring Boot projects
  - 在 Spring Boot 项目中编写或审查 Java 代码
- Enforcing naming, immutability, or exception handling conventions
  - 强制执行命名、不可变性或异常处理约定
- Working with records, sealed classes, or pattern matching (Java 17+)
  - 使用 record、sealed class 或模式匹配（Java 17+）
- Reviewing use of Optional, streams, or generics
  - 审查 Optional、流或泛型的使用
- Structuring packages and project layout
  - 构建包和项目布局

## Core Principles
## 核心原则

- Prefer clarity over cleverness
  - 优先考虑清晰而非聪明
- Immutable by default; minimize shared mutable state
  - 默认不可变；最小化共享可变状态
- Fail fast with meaningful exceptions
  - 用有意义的异常快速失败
- Consistent naming and package structure
  - 一致的命名和包结构

## Naming
## 命名

```java
// ✅ Classes/Records: PascalCase
public class MarketService {}
public record Money(BigDecimal amount, Currency currency) {}

// ✅ Methods/fields: camelCase
private final MarketRepository marketRepository;
public Market findBySlug(String slug) {}

// ✅ Constants: UPPER_SNAKE_CASE
private static final int MAX_PAGE_SIZE = 100;
```

// ✅ 类/Record：PascalCase
public class MarketService {}
public record Money(BigDecimal amount, Currency currency) {}

// ✅ 方法/字段：camelCase
private final MarketRepository marketRepository;
public Market findBySlug(String slug) {}

// ✅ 常量：UPPER_SNAKE_CASE
private static final int MAX_PAGE_SIZE = 100;

## Immutability
## 不可变性

```java
// ✅ Favor records and final fields
public record MarketDto(Long id, String name, MarketStatus status) {}

public class Market {
  private final Long id;
  private final String name;
  // getters only, no setters
}
```

// ✅ 优先使用 record 和 final 字段
public record MarketDto(Long id, String name, MarketStatus status) {}

public class Market {
  private final Long id;
  private final String name;
  // 只提供 getter，不提供 setter
}

## Optional Usage
## Optional 使用

```java
// ✅ Return Optional from find* methods
Optional<Market> market = marketRepository.findBySlug(slug);

// ✅ Map/flatMap instead of get()
return market
    .map(MarketResponse::from)
    .orElseThrow(() -> new EntityNotFoundException("Market not found"));
```

// ✅ 从 find* 方法返回 Optional
Optional<Market> market = marketRepository.findBySlug(slug);

// ✅ 使用 map/flatMap 而不是 get()
return market
    .map(MarketResponse::from)
    .orElseThrow(() -> new EntityNotFoundException("Market not found"));

## Streams Best Practices
## 流最佳实践

```java
// ✅ Use streams for transformations, keep pipelines short
List<String> names = markets.stream()
    .map(Market::name)
    .filter(Objects::nonNull)
    .toList();

// ❌ Avoid complex nested streams; prefer loops for clarity
```

// ✅ 使用流进行转换，保持管道简短
List<String> names = markets.stream()
    .map(Market::name)
    .filter(Objects::nonNull)
    .toList();

// ❌ 避免复杂的嵌套流；优先使用循环以保持清晰

## Exceptions
## 异常

- Use unchecked exceptions for domain errors; wrap technical exceptions with context
  - 对领域错误使用非受检异常；用上下文包装技术异常
- Create domain-specific exceptions (e.g., `MarketNotFoundException`)
  - 创建领域特定异常（例如 `MarketNotFoundException`）
- Avoid broad `catch (Exception ex)` unless rethrowing/logging centrally
  - 除非重新抛出/集中记录，否则避免宽泛的 `catch (Exception ex)`

```java
throw new MarketNotFoundException(slug);
```

throw new MarketNotFoundException(slug);

## Generics and Type Safety
## 泛型和类型安全

- Avoid raw types; declare generic parameters
  - 避免原始类型；声明泛型参数
- Prefer bounded generics for reusable utilities
  - 对于可重用工具，优先使用有界泛型

```java
public <T extends Identifiable> Map<Long, T> indexById(Collection<T> items) { ... }
```

public <T extends Identifiable> Map<Long, T> indexById(Collection<T> items) { ... }

## Project Structure (Maven/Gradle)
## 项目结构（Maven/Gradle）

```
src/main/java/com/example/app/
  config/
  controller/
  service/
  repository/
  domain/
  dto/
  util/
src/main/resources/
  application.yml
src/test/java/... (mirrors main)
```

src/main/java/com/example/app/
  config/
  controller/
  service/
  repository/
  domain/
  dto/
  util/
src/main/resources/
  application.yml
src/test/java/...（镜像 main）

## Formatting and Style
## 格式和样式

- Use 2 or 4 spaces consistently (project standard)
  - 一致使用 2 或 4 个空格（项目标准）
- One public top-level type per file
  - 每个文件一个公共顶级类型
- Keep methods short and focused; extract helpers
  - 保持方法简短且专注；提取辅助方法
- Order members: constants, fields, constructors, public methods, protected, private
  - 成员顺序：常量、字段、构造函数、公共方法、受保护方法、私有方法

## Code Smells to Avoid
## 应避免的代码气味

- Long parameter lists → use DTO/builders
  - 长参数列表 → 使用 DTO/构建器
- Deep nesting → early returns
  - 深层嵌套 → 提前返回
- Magic numbers → named constants
  - 魔法数字 → 命名常量
- Static mutable state → prefer dependency injection
  - 静态可变状态 → 优先使用依赖注入
- Silent catch blocks → log and act or rethrow
  - 静默捕获块 → 记录并行动或重新抛出

## Logging
## 日志记录

```java
private static final Logger log = LoggerFactory.getLogger(MarketService.class);
log.info("fetch_market slug={}", slug);
log.error("failed_fetch_market slug={}", slug, ex);
```

private static final Logger log = LoggerFactory.getLogger(MarketService.class);
log.info("fetch_market slug={}", slug);
log.error("failed_fetch_market slug={}", slug, ex);

## Null Handling
## 空值处理

- Accept `@Nullable` only when unavoidable; otherwise use `@NonNull`
  - 仅在不可避免时接受 `@Nullable`；否则使用 `@NonNull`
- Use Bean Validation (`@NotNull`, `@NotBlank`) on inputs
  - 在输入上使用 Bean Validation（`@NotNull`、`@NotBlank`）

## Testing Expectations
## 测试期望

- JUnit 5 + AssertJ for fluent assertions
  - JUnit 5 + AssertJ 用于流畅断言
- Mockito for mocking; avoid partial mocks where possible
  - Mockito 用于模拟；尽可能避免部分模拟
- Favor deterministic tests; no hidden sleeps
  - 优先使用确定性测试；不要有隐藏的 sleep

**Remember**: Keep code intentional, typed, and observable. Optimize for maintainability over micro-optimizations unless proven necessary.
**记住**：保持代码有目的、有类型、可观察。除非被证明必要，否则优先考虑可维护性而非微优化。
