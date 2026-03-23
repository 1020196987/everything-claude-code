---
name: rust-testing
description: Rust testing patterns including unit tests, integration tests, async testing, property-based testing, mocking, and coverage. Follows TDD methodology.
description zh-CN: Rust 测试模式，包括单元测试、集成测试、异步测试、属性测试、模拟和覆盖率。遵循 TDD 方法论。
origin: ECC
---

# Rust Testing Patterns

## Rust 测试模式

Comprehensive Rust testing patterns for writing reliable, maintainable tests following TDD methodology.

遵循 TDD 方法论编写可靠、可维护测试的综合 Rust 测试模式。

## When to Use

## When to Use
## 何时使用

- Writing new Rust functions, methods, or traits
  - 编写新的 Rust 函数、方法或 trait
- Adding test coverage to existing code
  - 为现有代码添加测试覆盖率
- Creating benchmarks for performance-critical code
  - 为性能关键代码创建基准测试
- Implementing property-based tests for input validation
  - 实现属性测试进行输入验证
- Following TDD workflow in Rust projects
  - 在 Rust 项目中遵循 TDD 工作流

## How It Works

## How It Works
## 工作原理

1. **Identify target code** — Find the function, trait, or module to test
   - **识别目标代码** — 找到要测试的函数、trait 或模块
2. **Write a test** — Use `#[test]` in a `#[cfg(test)]` module, rstest for parameterized tests, or proptest for property-based tests
   - **编写测试** — 在 `#[cfg(test)]` 模块中使用 `#[test]`，参数化测试用 rstest，属性测试用 proptest
3. **Mock dependencies** — Use mockall to isolate the unit under test
   - **模拟依赖** — 使用 mockall 隔离被测单元
4. **Run tests (RED)** — Verify the test fails with the expected error
   - **运行测试（RED）** — 验证测试以预期错误失败
5. **Implement (GREEN)** — Write minimal code to pass
   - **实现（GREEN）** — 编写最小代码通过
6. **Refactor** — Improve while keeping tests green
   - **重构** — 保持测试绿色的同时改进
7. **Check coverage** — Use cargo-llvm-cov, target 80%+
   - **检查覆盖率** — 使用 cargo-llvm-cov，目标 80%+

## TDD Workflow for Rust

## TDD Workflow for Rust
## Rust TDD 工作流

### The RED-GREEN-REFACTOR Cycle

### The RED-GREEN-REFACTOR Cycle
### 红-绿-重构循环

```
RED     → Write a failing test first
RED     → 首先编写一个失败的测试
GREEN   → Write minimal code to pass the test
GREEN   → 编写最小代码使测试通过
REFACTOR → Improve code while keeping tests green
REFACTOR → 在保持测试绿色的同时改进代码
REPEAT  → Continue with next requirement
REPEAT  → 用下一个需求继续
```

### Step-by-Step TDD in Rust

### Step-by-Step TDD in Rust
### Rust 中的逐步 TDD

```rust
// RED: Write test first, use todo!() as placeholder
// RED：首先编写测试，用 todo!() 作为占位符
pub fn add(a: i32, b: i32) -> i32 { todo!() }

#[cfg(test)]
mod tests {
    use super::*;
    #[test]
    fn test_add() { assert_eq!(add(2, 3), 5); }
}
// cargo test → panics at 'not yet implemented'
```

```rust
// GREEN: Replace todo!() with minimal implementation
// GREEN：用最小实现替换 todo!()
pub fn add(a: i32, b: i32) -> i32 { a + b }
// cargo test → PASS, then REFACTOR while keeping tests green
```

## Unit Tests

## Unit Tests
## 单元测试

### Module-Level Test Organization

### Module-Level Test Organization
### 模块级测试组织

```rust
// src/user.rs
pub struct User {
    pub name: String,
    pub email: String,
}

impl User {
    pub fn new(name: impl Into<String>, email: impl Into<String>) -> Result<Self, String> {
        let email = email.into();
        if !email.contains('@') {
            return Err(format!("invalid email: {email}"));
        }
        Ok(Self { name: name.into(), email })
    }

    pub fn display_name(&self) -> &str {
        &self.name
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn creates_user_with_valid_email() {
        let user = User::new("Alice", "alice@example.com").unwrap();
        assert_eq!(user.display_name(), "Alice");
        assert_eq!(user.email, "alice@example.com");
    }

    #[test]
    fn rejects_invalid_email() {
        let result = User::new("Bob", "not-an-email");
        assert!(result.is_err());
        assert!(result.unwrap_err().contains("invalid email"));
    }
}
```

### Assertion Macros

### Assertion Macros
### 断言宏

```rust
assert_eq!(2 + 2, 4);                                    // Equality 相等
assert_ne!(2 + 2, 5);                                    // Inequality 不相等
assert!(vec![1, 2, 3].contains(&2));                     // Boolean 布尔
assert_eq!(value, 42, "expected 42 but got {value}");    // Custom message 自定义消息
assert!((0.1_f64 + 0.2 - 0.3).abs() < f64::EPSILON);   // Float comparison 浮点比较
```

## Error and Panic Testing

## Error and Panic Testing
## 错误和 Panic 测试

### Testing `Result` Returns

### Testing `Result` Returns
### 测试 `Result` 返回

```rust
#[test]
fn parse_returns_error_for_invalid_input() {
    let result = parse_config("}{invalid");
    assert!(result.is_err());

    // Assert specific error variant
    // 断言特定错误变体
    let err = result.unwrap_err();
    assert!(matches!(err, ConfigError::ParseError(_)));
}

#[test]
fn parse_succeeds_for_valid_input() -> Result<(), Box<dyn std::error::Error>> {
    let config = parse_config(r#"{"port": 8080}"#)?;
    assert_eq!(config.port, 8080);
    Ok(()) // Test fails if any ? returns Err
}
```

### Testing Panics

### Testing Panics
### 测试 Panic

```rust
#[test]
#[should_panic]
fn panics_on_empty_input() {
    process(&[]);
}

#[test]
#[should_panic(expected = "index out of bounds")]
fn panics_with_specific_message() {
    let v: Vec<i32> = vec![];
    let _ = v[0];
}
```

## Integration Tests

## Integration Tests
## 集成测试

### File Structure

### File Structure
### 文件结构

```text
my_crate/
├── src/
│   └── lib.rs
├── tests/              # Integration tests
│   ├── api_test.rs     # Each file is a separate test binary
│   ├── db_test.rs
│   └── common/         # Shared test utilities
│       └── mod.rs
```

### Writing Integration Tests

### Writing Integration Tests
### 编写集成测试

```rust
// tests/api_test.rs
use my_crate::{App, Config};

#[test]
fn full_request_lifecycle() {
    let config = Config::test_default();
    let app = App::new(config);

    let response = app.handle_request("/health");
    assert_eq!(response.status, 200);
    assert_eq!(response.body, "OK");
}
```

## Async Tests

## Async Tests
## 异步测试

### With Tokio

### With Tokio
### 使用 Tokio

```rust
#[tokio::test]
async fn fetches_data_successfully() {
    let client = TestClient::new().await;
    let result = client.get("/data").await;
    assert!(result.is_ok());
    assert_eq!(result.unwrap().items.len(), 3);
}

#[tokio::test]
async fn handles_timeout() {
    use std::time::Duration;
    let result = tokio::time::timeout(
        Duration::from_millis(100),
        slow_operation(),
    ).await;

    assert!(result.is_err(), "should have timed out");
}
```

## Test Organization Patterns

## Test Organization Patterns
## 测试组织模式

### Parameterized Tests with `rstest`

### Parameterized Tests with `rstest`
### 使用 `rstest` 参数化测试

```rust
use rstest::{rstest, fixture};

#[rstest]
#[case("hello", 5)]
#[case("", 0)]
#[case("rust", 4)]
fn test_string_length(#[case] input: &str, #[case] expected: usize) {
    assert_eq!(input.len(), expected);
}

// Fixtures
#[fixture]
fn test_db() -> TestDb {
    TestDb::new_in_memory()
}

#[rstest]
fn test_insert(test_db: TestDb) {
    test_db.insert("key", "value");
    assert_eq!(test_db.get("key"), Some("value".into()));
}
```

### Test Helpers

### Test Helpers
### 测试辅助函数

```rust
#[cfg(test)]
mod tests {
    use super::*;

    /// Creates a test user with sensible defaults.
    /// 创建带有合理默认值的测试用户。
    fn make_user(name: &str) -> User {
        User::new(name, &format!("{name}@test.com")).unwrap()
    }

    #[test]
    fn user_display() {
        let user = make_user("alice");
        assert_eq!(user.display_name(), "alice");
    }
}
```

## Property-Based Testing with `proptest`

## Property-Based Testing with `proptest`
## 使用 `proptest` 属性测试

### Basic Property Tests

### Basic Property Tests
### 基本属性测试

```rust
use proptest::prelude::*;

proptest! {
    #[test]
    fn encode_decode_roundtrip(input in ".*") {
        let encoded = encode(&input);
        let decoded = decode(&encoded).unwrap();
        assert_eq!(input, decoded);
    }

    #[test]
    fn sort_preserves_length(mut vec in prop::collection::vec(any::<i32>(), 0..100)) {
        let original_len = vec.len();
        vec.sort();
        assert_eq!(vec.len(), original_len);
    }

    #[test]
    fn sort_produces_ordered_output(mut vec in prop::collection::vec(any::<i32>(), 0..100)) {
        vec.sort();
        for window in vec.windows(2) {
            assert!(window[0] <= window[1]);
        }
    }
}
```

### Custom Strategies

### Custom Strategies
### 自定义策略

```rust
use proptest::prelude::*;

fn valid_email() -> impl Strategy<Value = String> {
    ("[a-z]{1,10}", "[a-z]{1,5}")
        .prop_map(|(user, domain)| format!("{user}@{domain}.com"))
}

proptest! {
    #[test]
    fn accepts_valid_emails(email in valid_email()) {
        assert!(User::new("Test", &email).is_ok());
    }
}
```

## Mocking with `mockall`

## Mocking with `mockall`
## 使用 `mockall` 模拟

### Trait-Based Mocking

### Trait-Based Mocking
### 基于 Trait 的模拟

```rust
use mockall::{automock, predicate::eq};

#[automock]
trait UserRepository {
    fn find_by_id(&self, id: u64) -> Option<User>;
    fn save(&self, user: &User) -> Result<(), StorageError>;
}

#[test]
fn service_returns_user_when_found() {
    let mut mock = MockUserRepository::new();
    mock.expect_find_by_id()
        .with(eq(42))
        .times(1)
        .returning(|_| Some(User { id: 42, name: "Alice".into() }));

    let service = UserService::new(Box::new(mock));
    let user = service.get_user(42).unwrap();
    assert_eq!(user.name, "Alice");
}

#[test]
fn service_returns_none_when_not_found() {
    let mut mock = MockUserRepository::new();
    mock.expect_find_by_id()
        .returning(|_| None);

    let service = UserService::new(Box::new(mock));
    assert!(service.get_user(99).is_none());
}
```

## Doc Tests

## Doc Tests
## 文档测试

### Executable Documentation

### Executable Documentation
### 可执行文档

```rust
/// Adds two numbers together.
///
/// # Examples
///
/// ```
/// use my_crate::add;
///
/// assert_eq!(add(2, 3), 5);
/// assert_eq!(add(-1, 1), 0);
/// ```
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}

/// Parses a config string.
///
/// # Errors
///
/// Returns `Err` if the input is not valid TOML.
///
/// ```no_run
/// use my_crate::parse_config;
///
/// let config = parse_config(r#"port = 8080"#).unwrap();
/// assert_eq!(config.port, 8080);
/// ```
///
/// ```no_run
/// use my_crate::parse_config;
///
/// assert!(parse_config("}{invalid").is_err());
/// ```
pub fn parse_config(input: &str) -> Result<Config, ParseError> {
    todo!()
}
```

## Benchmarking with Criterion

## Benchmarking with Criterion
## 使用 Criterion 基准测试

```toml
# Cargo.toml
[dev-dependencies]
criterion = { version = "0.5", features = ["html_reports"] }

[[bench]]
name = "benchmark"
harness = false
```

```rust
// benches/benchmark.rs
use criterion::{black_box, criterion_group, criterion_main, Criterion};

fn fibonacci(n: u64) -> u64 {
    match n {
        0 | 1 => n,
        _ => fibonacci(n - 1) + fibonacci(n - 2),
    }
}

fn bench_fibonacci(c: &mut Criterion) {
    c.bench_function("fib 20", |b| b.iter(|| fibonacci(black_box(20))));
}

criterion_group!(benches, bench_fibonacci);
criterion_main!(benches);
```

## Test Coverage

## Test Coverage
## 测试覆盖率

### Running Coverage

### Running Coverage
### 运行覆盖率

```bash
# Install: cargo install cargo-llvm-cov (or use taiki-e/install-action in CI)
# 安装：cargo install cargo-llvm-cov（或在 CI 中使用 taiki-e/install-action）
cargo llvm-cov                    # Summary 摘要
cargo llvm-cov --html             # HTML report HTML 报告
cargo llvm-cov --lcov > lcov.info # LCOV format for CI LCOV 格式用于 CI
cargo llvm-cov --fail-under-lines 80  # Fail if below threshold 如果低于阈值则失败
```

### Coverage Targets

### Coverage Targets
### 覆盖率目标

| Code Type | Target | 代码类型 | 目标 |
|-----------|--------|----------|------|
| Critical business logic | 100% | 关键业务逻辑 | 100% |
| Public API | 90%+ | 公共 API | 90%+ |
| General code | 80%+ | 一般代码 | 80%+ |
| Generated / FFI bindings | Exclude | 生成代码 / FFI 绑定 | 排除 |

## Testing Commands

## Testing Commands
## 测试命令

```bash
cargo test                        # Run all tests 运行所有测试
cargo test -- --nocapture         # Show println output 显示 println 输出
cargo test test_name               # Run tests matching pattern 运行匹配模式的测试
cargo test --lib                  # Unit tests only 仅单元测试
cargo test --test api_test        # Integration tests only 仅集成测试
cargo test --doc                 # Doc tests only 仅文档测试
cargo test --no-fail-fast         # Don't stop on first failure 不在首次失败时停止
cargo test -- --ignored           # Run ignored tests 运行被忽略的测试
```

## Best Practices

## Best Practices
## 最佳实践

**DO:**
**应该做：**
- Write tests FIRST (TDD)
  - 首先编写测试（TDD）
- Use `#[cfg(test)]` modules for unit tests
  - 单元测试使用 `#[cfg(test)]` 模块
- Test behavior, not implementation
  - 测试行为而非实现
- Use descriptive test names that explain the scenario
  - 使用描述性测试名称说明场景
- Prefer `assert_eq!` over `assert!` for better error messages
  - 为更好的错误消息优先使用 `assert_eq!` 而非 `assert!`
- Use `?` in tests that return `Result` for cleaner error output
  - 返回 `Result` 的测试中使用 `?` 以获得更清晰的错误输出
- Keep tests independent — no shared mutable state
  - 保持测试独立 — 不共享可变状态

**DON'T:**
**不应该做：**
- Use `#[should_panic]` when you can test `Result::is_err()` instead
  - 能测试 `Result::is_err()` 时不使用 `#[should_panic]`
- Mock everything — prefer integration tests when feasible
  - 不要模拟一切 — 可行时优先使用集成测试
- Ignore flaky tests — fix or quarantine them
  - 不要忽略不稳定测试 — 修复或隔离它们
- Use `sleep()` in tests — use channels, barriers, or `tokio::time::pause()`
  - 测试中不要用 `sleep()` — 使用通道、屏障或 `tokio::time::pause()`
- Skip error path testing
  - 不要跳过错误路径测试

## CI Integration

## CI Integration
## CI 集成

```yaml
# GitHub Actions
test:
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v4
    - uses: dtolnay/rust-toolchain@stable
      with:
        components: clippy, rustfmt

    - name: Check formatting
      run: cargo fmt --check

    - name: Clippy
      run: cargo clippy -- -D warnings

    - name: Run tests
      run: cargo test

    - uses: taiki-e/install-action@cargo-llvm-cov

    - name: Coverage
      run: cargo llvm-cov --fail-under-lines 80
```

**Remember**: Tests are documentation. They show how your code is meant to be used. Write them clearly and keep them up to date.

**记住**：测试即文档。它们展示你的代码应如何使用。清晰地编写并保持更新。
