---
description: Enforce TDD workflow for Rust. Write tests first, then implement. Verify 80%+ coverage with cargo-llvm-cov.
description-zh: 强制 Rust 的 TDD 工作流。先写测试，然后实现。用 cargo-llvm-cov 验证 80%+ 覆盖率。
---

# Rust TDD Command
# Rust TDD 命令

This command enforces test-driven development methodology for Rust code using `#[test]`, rstest, proptest, and mockall.
此命令使用 `#[test]`、rstest、proptest 和 mockall 对 Rust 代码强制执行测试驱动开发方法论。

## What This Command Does
## 此命令做什么

1. **Define Types/Traits**: Scaffold function signatures with `todo!()`
1. **定义类型/Traits**：用 `todo!()` 搭建函数签名

2. **Write Tests**: Create comprehensive test module (RED)
2. **写测试**：创建全面的测试模块（RED）

3. **Run Tests**: Verify tests fail for the right reason
3. **运行测试**：验证测试因正确原因失败

4. **Implement Code**: Write minimal code to pass (GREEN)
4. **实现代码**：编写最少的通过代码（GREEN）

5. **Refactor**: Improve while keeping tests green
5. **重构**：在保持测试通过的情况下改进

6. **Check Coverage**: Ensure 80%+ coverage with cargo-llvm-cov
6. **检查覆盖率**：用 cargo-llvm-cov 确保 80%+ 覆盖率

## When to Use
## 使用场景

Use `/rust-test` when:
使用 `/rust-test` 当：

- Implementing new Rust functions, methods, or traits
- 实现新的 Rust 函数、方法或 traits

- Adding test coverage to existing Rust code
- 为现有 Rust 代码添加测试覆盖率

- Fixing bugs (write failing test first)
- 修复 bug（先写失败的测试）

- Building critical business logic
- 构建关键业务逻辑

- Learning TDD workflow in Rust
- 学习 Rust 中的 TDD 工作流

## TDD Cycle
## TDD 循环

```
RED     -> Write failing test first
RED     -> 先写失败的测试
GREEN   -> Implement minimal code to pass
GREEN   -> 实现最少的通过代码
REFACTOR -> Improve code, tests stay green
REFACTOR -> 改进代码，测试保持通过
REPEAT  -> Next test case
REPEAT  -> 下一个测试用例
```

## Example Session
## 示例会话

````text
User: /rust-test I need a function to validate user registration

Agent:
# TDD Session: Registration Validator

## Step 1: Define Interface

```rust
// src/validator.rs
pub struct RegistrationRequest {
    pub name: String,
    pub email: String,
    pub password: String,
}

pub enum ValidationResult {
    Valid,
    Invalid(Vec<String>),
}

pub fn validate_registration(request: &RegistrationRequest) -> ValidationResult {
    todo!()
}
```

## Step 2: Write Tests (RED)

```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn valid_registration_returns_valid() {
        let request = RegistrationRequest {
            name: "Alice".into(),
            email: "alice@example.com".into(),
            password: "SecureP@ss1".into(),
        };
        assert!(matches!(validate_registration(&request), ValidationResult::Valid));
    }

    #[test]
    fn blank_name_returns_invalid() {
        let request = RegistrationRequest {
            name: "".into(),
            email: "alice@example.com".into(),
            password: "SecureP@ss1".into(),
        };
        match validate_registration(&request) {
            ValidationResult::Invalid(errors) => {
                assert!(errors.contains(&"Name is required".to_string()));
            }
            ValidationResult::Valid => panic!("expected Invalid"),
        }
    }

    #[test]
    fn invalid_email_returns_invalid() {
        let request = RegistrationRequest {
            name: "Alice".into(),
            email: "not-an-email".into(),
            password: "SecureP@ss1".into(),
        };
        assert!(matches!(
            validate_registration(&request),
            ValidationResult::Invalid(_)
        ));
    }

    #[test]
    fn short_password_returns_invalid() {
        let request = RegistrationRequest {
            name: "Alice".into(),
            email: "alice@example.com".into(),
            password: "short".into(),
        };
        assert!(matches!(
            validate_registration(&request),
            ValidationResult::Invalid(_)
        ));
    }
}
```

## Step 3: Run Tests — Verify FAIL

```bash
$ cargo test
running 4 tests
test tests::valid_registration_returns_valid ... FAILED (not yet implemented)
test tests::blank_name_returns_invalid ... FAILED
test tests::invalid_email_returns_invalid ... FAILED
test tests::short_password_returns_invalid ... FAILED
```

Tests fail as expected (todo! panic).

## Step 4: Implement Minimal Code (GREEN)

```rust
pub fn validate_registration(request: &RegistrationRequest) -> ValidationResult {
    let mut errors = Vec::new();

    if request.name.trim().is_empty() {
        errors.push("Name is required".into());
    }
    if !request.email.contains('@') {
        errors.push("Invalid email format".into());
    }
    if request.password.len() < 8 {
        errors.push("Password must be at least 8 characters".into());
    }

    if errors.is_empty() {
        ValidationResult::Valid
    } else {
        ValidationResult::Invalid(errors)
    }
}
```

## Step 5: Run Tests — Verify PASS

```bash
$ cargo test
running 4 tests
test tests::valid_registration_returns_valid ... ok
test tests::blank_name_returns_invalid ... ok
test tests::invalid_email_returns_invalid ... ok
test tests::short_password_returns_invalid ... ok

test result: ok. 4 passed; 0 failed
```

All tests passing!

## Step 6: Check Coverage

```bash
$ cargo llvm-cov
Coverage: 100.0% of lines
```

Coverage: 100%

## TDD Complete!
````

## Test Patterns
## 测试模式

### Unit Tests
### 单元测试

```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn adds_two_numbers() {
        assert_eq!(add(2, 3), 5);
    }

    #[test]
    fn handles_error() -> Result<(), Box<dyn std::error::Error>> {
        let result = parse_config(r#"port = 8080"#)?;
        assert_eq!(result.port, 8080);
        Ok(())
    }
}
```

### Parameterized Tests with rstest
### 使用 rstest 的参数化测试

```rust
use rstest::{rstest, fixture};

#[rstest]
#[case("hello", 5)]
#[case("", 0)]
#[case("rust", 4)]
fn test_string_length(#[case] input: &str, #[case] expected: usize) {
    assert_eq!(input.len(), expected);
}
```

### Async Tests
### 异步测试

```rust
#[tokio::test]
async fn fetches_data_successfully() {
    let client = TestClient::new().await;
    let result = client.get("/data").await;
    assert!(result.is_ok());
}
```

### Property-Based Tests
### 属性测试

```rust
use proptest::prelude::*;

proptest! {
    #[test]
    fn encode_decode_roundtrip(input in ".*") {
        let encoded = encode(&input);
        let decoded = decode(&encoded).unwrap();
        assert_eq!(input, decoded);
    }
}
```

## Coverage Commands
## 覆盖率命令

```bash
# Summary report
# 摘要报告
cargo llvm-cov

# HTML report
# HTML 报告
cargo llvm-cov --html

# Fail if below threshold
# 低于阈值时失败
cargo llvm-cov --fail-under-lines 80

# Run specific test
# 运行特定测试
cargo test test_name

# Run with output
# 带输出运行
cargo test -- --nocapture

# Run without stopping on first failure
# 不在首次失败时停止
cargo test --no-fail-fast
```

## Coverage Targets
## 覆盖率目标

| Code Type | Target |
|-----------|--------|
| Critical business logic | 100% |
| 关键业务逻辑 | 100% |
| Public API | 90%+ |
| 公共 API | 90%+ |
| General code | 80%+ |
| 一般代码 | 80%+ |
| Generated / FFI bindings | Exclude |
| 生成代码 / FFI 绑定 | 排除 |

## TDD Best Practices
## TDD 最佳实践

**DO:**
**应该：**

- Write test FIRST, before any implementation
- 先写测试，再实现

- Run tests after each change
- 每次更改后运行测试

- Use `assert_eq!` over `assert!` for better error messages
- 使用 `assert_eq!` 而不是 `assert!` 以获得更好的错误消息

- Use `?` in tests that return `Result` for cleaner output
- 在返回 `Result` 的测试中使用 `?` 以获得更清晰的输出

- Test behavior, not implementation
- 测试行为，而不是实现

- Include edge cases (empty, boundary, error paths)
- 包含边缘情况（空、边界、错误路径）

**DON'T:**
**不应该：**

- Write implementation before tests
- 先实现再写测试

- Skip the RED phase
- 跳过 RED 阶段

- Use `#[should_panic]` when `Result::is_err()` works
- 当 `Result::is_err()` 可用时使用 `#[should_panic]`

- Use `sleep()` in tests — use channels or `tokio::time::pause()`
- 在测试中使用 `sleep()` — 使用 channels 或 `tokio::time::pause()`

- Mock everything — prefer integration tests when feasible
- Mock 一切 — 尽可能优先使用集成测试

## Related Commands
## 相关命令

- `/rust-build` - Fix build errors
- `/rust-build` - 修复构建错误

- `/rust-review` - Review code after implementation
- `/rust-review` - 实现后审查代码

- `/verify` - Run full verification loop
- `/verify` - 运行完整验证循环

## Related
## 相关

- Skill: `skills/rust-testing/`
- Skill: `skills/rust-patterns/`
