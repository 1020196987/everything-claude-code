---
description: Enforce TDD workflow for Kotlin. Write Kotest tests first, then implement. Verify 80%+ coverage with Kover.
description-zh: 强制 Kotlin 的 TDD 工作流。先写 Kotest 测试，然后实现。用 Kover 验证 80%+ 覆盖率。
---

# Kotlin TDD Command
# Kotlin TDD 命令

This command enforces test-driven development methodology for Kotlin code using Kotest, MockK, and Kover.
此命令使用 Kotest、MockK 和 Kover 对 Kotlin 代码强制执行测试驱动开发方法论。

## What This Command Does
## 此命令做什么

1. **Define Types/Interfaces**: Scaffold function signatures first
1. **定义类型/接口**：先搭建函数签名

2. **Write Kotest Tests**: Create comprehensive test specs (RED)
2. **写 Kotest 测试**：创建全面的测试规范（RED）

3. **Run Tests**: Verify tests fail for the right reason
3. **运行测试**：验证测试因正确原因失败

4. **Implement Code**: Write minimal code to pass (GREEN)
4. **实现代码**：编写最少的通过代码（GREEN）

5. **Refactor**: Improve while keeping tests green
5. **重构**：在保持测试通过的情况下改进

6. **Check Coverage**: Ensure 80%+ coverage with Kover
6. **检查覆盖率**：用 Kover 确保 80%+ 覆盖率

## When to Use
## 使用场景

Use `/kotlin-test` when:
使用 `/kotlin-test` 当：

- Implementing new Kotlin functions or classes
- 实现新的 Kotlin 函数或类

- Adding test coverage to existing Kotlin code
- 为现有 Kotlin 代码添加测试覆盖率

- Fixing bugs (write failing test first)
- 修复 bug（先写失败的测试）

- Building critical business logic
- 构建关键业务逻辑

- Learning TDD workflow in Kotlin
- 学习 Kotlin 中的 TDD 工作流

## TDD Cycle
## TDD 循环

```
RED     -> Write failing Kotest test
RED     -> 编写失败的 Kotest 测试
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
User: /kotlin-test I need a function to validate user registration requests

Agent:
# TDD Session: Registration Validator

## Step 1: Define Interface

```kotlin
// validator/RegistrationValidator.kt
package com.example.validator

data class RegistrationRequest(
    val name: String,
    val email: String,
    val password: String,
)

sealed class ValidationResult {
    data object Valid : ValidationResult()
    data class Invalid(val errors: List<String>) : ValidationResult()
}

fun validateRegistration(request: RegistrationRequest): ValidationResult {
    TODO("not implemented")
}
```

## Step 2: Write Kotest Tests (RED)

```kotlin
// validator/RegistrationValidatorTest.kt
package com.example.validator

import io.kotest.core.spec.style.FunSpec
import io.kotest.matchers.shouldBe
import io.kotest.matchers.types.shouldBeInstanceOf

class RegistrationValidatorTest : FunSpec({
    test("valid registration returns Valid") {
        val request = RegistrationRequest(
            name = "Alice",
            email = "alice@example.com",
            password = "SecureP@ss1",
        )

        val result = validateRegistration(request)

        result.shouldBeInstanceOf<ValidationResult.Valid>()
    }

    test("blank name returns Invalid") {
        val request = RegistrationRequest(
            name = "",
            email = "alice@example.com",
            password = "SecureP@ss1",
        )

        val result = validateRegistration(request)

        val invalid = result.shouldBeInstanceOf<ValidationResult.Invalid>()
        invalid.errors shouldBe listOf("Name is required")
    }

    test("invalid email returns Invalid") {
        val request = RegistrationRequest(
            name = "Alice",
            email = "not-an-email",
            password = "SecureP@ss1",
        )

        val result = validateRegistration(request)

        val invalid = result.shouldBeInstanceOf<ValidationResult.Invalid>()
        invalid.errors shouldBe listOf("Invalid email format")
    }

    test("short password returns Invalid") {
        val request = RegistrationRequest(
            name = "Alice",
            email = "alice@example.com",
            password = "short",
        )

        val result = validateRegistration(request)

        val invalid = result.shouldBeInstanceOf<ValidationResult.Invalid>()
        invalid.errors shouldBe listOf("Password must be at least 8 characters")
    }

    test("multiple errors returns all errors") {
        val request = RegistrationRequest(
            name = "",
            email = "bad",
            password = "short",
        )

        val result = validateRegistration(request)

        val invalid = result.shouldBeInstanceOf<ValidationResult.Invalid>()
        invalid.errors.size shouldBe 3
    }
})
```

## Step 3: Run Tests - Verify FAIL

```bash
$ ./gradlew test

RegistrationValidatorTest > valid registration returns Valid FAILED
  kotlin.NotImplementedError: An operation is not implemented

FAILED (5 tests, 0 passed, 5 failed)
```

✓ Tests fail as expected (NotImplementedError).

## Step 4: Implement Minimal Code (GREEN)

```kotlin
// validator/RegistrationValidator.kt
package com.example.validator

private val EMAIL_REGEX = Regex("^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,}$")
private const val MIN_PASSWORD_LENGTH = 8

fun validateRegistration(request: RegistrationRequest): ValidationResult {
    val errors = buildList {
        if (request.name.isBlank()) add("Name is required")
        if (!EMAIL_REGEX.matches(request.email)) add("Invalid email format")
        if (request.password.length < MIN_PASSWORD_LENGTH) add("Password must be at least $MIN_PASSWORD_LENGTH characters")
    }

    return if (errors.isEmpty()) ValidationResult.Valid
    else ValidationResult.Invalid(errors)
}
```

## Step 5: Run Tests - Verify PASS

```bash
$ ./gradlew test

RegistrationValidatorTest > valid registration returns Valid PASSED
RegistrationValidatorTest > blank name returns Invalid PASSED
RegistrationValidatorTest > invalid email returns Invalid PASSED
RegistrationValidatorTest > short password returns Invalid PASSED
RegistrationValidatorTest > multiple errors returns all errors PASSED

PASSED (5 tests, 5 passed, 0 failed)
```

✓ All tests passing!

## Step 6: Check Coverage

```bash
$ ./gradlew koverHtmlReport

Coverage: 100.0% of statements
```

✓ Coverage: 100%

## TDD Complete!
````

## Test Patterns
## 测试模式

### StringSpec (Simplest)
### StringSpec（最简单）

```kotlin
class CalculatorTest : StringSpec({
    "add two positive numbers" {
        Calculator.add(2, 3) shouldBe 5
    }
})
```

### BehaviorSpec (BDD)
### BehaviorSpec（BDD）

```kotlin
class OrderServiceTest : BehaviorSpec({
    Given("a valid order") {
        When("placed") {
            Then("should be confirmed") { /* ... */ }
        }
    }
})
```

### Data-Driven Tests
### 数据驱动测试

```kotlin
class ParserTest : FunSpec({
    context("valid inputs") {
        withData("2026-01-15", "2026-12-31", "2000-01-01") { input ->
            parseDate(input).shouldNotBeNull()
        }
    }
})
```

### Coroutine Testing
### 协程测试

```kotlin
class AsyncServiceTest : FunSpec({
    test("concurrent fetch completes") {
        runTest {
            val result = service.fetchAll()
            result.shouldNotBeEmpty()
        }
    }
})
```

## Coverage Commands
## 覆盖率命令

```bash
# Run tests with coverage
# 用覆盖率运行测试
./gradlew koverHtmlReport

# Verify coverage thresholds
# 验证覆盖率阈值
./gradlew koverVerify

# XML report for CI
# CI 用 XML 报告
./gradlew koverXmlReport

# Open HTML report
# 打开 HTML 报告
open build/reports/kover/html/index.html

# Run specific test class
# 运行特定测试类
./gradlew test --tests "com.example.UserServiceTest"

# Run with verbose output
# 带详细输出运行
./gradlew test --info
```

## Coverage Targets
## 覆盖率目标

| Code Type | Target |
|-----------|--------|
| Critical business logic | 100% |
| 关键业务逻辑 | 100% |
| Public APIs | 90%+ |
| 公共 API | 90%+ |
| General code | 80%+ |
| 一般代码 | 80%+ |
| Generated code | Exclude |
| 生成代码 | 排除 |

## TDD Best Practices
## TDD 最佳实践

**DO:**
**应该：**

- Write test FIRST, before any implementation
- 先写测试，再实现

- Run tests after each change
- 每次更改后运行测试

- Use Kotest matchers for expressive assertions
- 使用 Kotest 匹配器进行表达性断言

- Use MockK's `coEvery`/`coVerify` for suspend functions
- 对 suspend 函数使用 MockK 的 `coEvery`/`coVerify`

- Test behavior, not implementation details
- 测试行为，而不是实现细节

- Include edge cases (empty, null, max values)
- 包含边缘情况（空、null、最大值）

**DON'T:**
**不应该：**

- Write implementation before tests
- 先实现再写测试

- Skip the RED phase
- 跳过 RED 阶段

- Test private functions directly
- 直接测试私有函数

- Use `Thread.sleep()` in coroutine tests
- 在协程测试中使用 `Thread.sleep()`

- Ignore flaky tests
- 忽略不稳定测试

## Related Commands
## 相关命令

- `/kotlin-build` - Fix build errors
- `/kotlin-build` - 修复构建错误

- `/kotlin-review` - Review code after implementation
- `/kotlin-review` - 实现后审查代码

- `/verify` - Run full verification loop
- `/verify` - 运行完整验证循环

## Related
## 相关

- Skill: `skills/kotlin-testing/`
- Skill: `skills/tdd-workflow/`
