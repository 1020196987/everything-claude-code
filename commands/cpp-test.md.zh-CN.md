---
description: Enforce TDD workflow for C++. Write GoogleTest tests first, then implement. Verify coverage with gcov/lcov.
description-zh: 强制 C++ 的 TDD 工作流。先写 GoogleTest 测试，然后实现。用 gcov/lcov 验证覆盖率。
---

# C++ TDD Command
# C++ TDD 命令

This command enforces test-driven development methodology for C++ code using GoogleTest/GoogleMock with CMake/CTest.
此命令使用 GoogleTest/GoogleMock 和 CMake/CTest 对 C++ 代码强制执行测试驱动开发方法论。

## What This Command Does
## 此命令做什么

1. **Define Interfaces**: Scaffold class/function signatures first
1. **定义接口**：先搭建类/函数签名

2. **Write Tests**: Create comprehensive GoogleTest test cases (RED)
2. **写测试**：创建全面的 GoogleTest 测试用例（RED）

3. **Run Tests**: Verify tests fail for the right reason
3. **运行测试**：验证测试因正确原因失败

4. **Implement Code**: Write minimal code to pass (GREEN)
4. **实现代码**：编写最少的通过代码（GREEN）

5. **Refactor**: Improve while keeping tests green
5. **重构**：在保持测试通过的情况下改进

6. **Check Coverage**: Ensure 80%+ coverage
6. **检查覆盖率**：确保 80%+ 覆盖率

## When to Use
## 使用场景

Use `/cpp-test` when:
使用 `/cpp-test` 当：

- Implementing new C++ functions or classes
- 实现新的 C++ 函数或类

- Adding test coverage to existing code
- 为现有代码添加测试覆盖率

- Fixing bugs (write failing test first)
- 修复 bug（先写失败的测试）

- Building critical business logic
- 构建关键业务逻辑

- Learning TDD workflow in C++
- 学习 C++ 中的 TDD 工作流

## TDD Cycle
## TDD 循环

```
RED     → Write failing GoogleTest test
RED     → 编写失败的 GoogleTest 测试
GREEN   → Implement minimal code to pass
GREEN   → 实现最少的通过代码
REFACTOR → Improve code, tests stay green
REFACTOR → 改进代码，测试保持通过
REPEAT  → Next test case
REPEAT  → 下一个测试用例
```

## Example Session
## 示例会话

````
User: /cpp-test I need a function to validate email addresses

Agent:
# TDD Session: Email Validator

## Step 1: Define Interface

```cpp
// validator/email.hpp
#pragma once
#include <string>
#include <expected>

enum class EmailError {
    Empty,
    InvalidFormat
};

std::expected<void, EmailError> validate_email(const std::string& email);
```

## Step 2: Write Tests (RED)

```cpp
// validator/email_test.cpp
#include <gtest/gtest.h>
#include "email.hpp"

TEST(ValidateEmail, AcceptsSimpleEmail) {
    auto result = validate_email("user@example.com");
    EXPECT_TRUE(result.has_value());
}

TEST(ValidateEmail, AcceptsSubdomain) {
    EXPECT_TRUE(validate_email("user@mail.example.com").has_value());
}

TEST(ValidateEmail, AcceptsPlus) {
    EXPECT_TRUE(validate_email("user+tag@example.com").has_value());
}

TEST(ValidateEmail, RejectsEmpty) {
    auto result = validate_email("");
    ASSERT_FALSE(result.has_value());
    EXPECT_EQ(result.error(), EmailError::Empty);
}

TEST(ValidateEmail, RejectsNoAtSign) {
    EXPECT_FALSE(validate_email("userexample.com").has_value());
}

TEST(ValidateEmail, RejectsNoDomain) {
    EXPECT_FALSE(validate_email("user@").has_value());
}

TEST(ValidateEmail, RejectsNoLocalPart) {
    EXPECT_FALSE(validate_email("@example.com").has_value());
}
```

## Step 3: Run Tests - Verify FAIL

```bash
$ cmake --build build && ctest --test-dir build --output-on-failure

1/1 Test #1: email_validator_test .....***Failed
    --- undefined reference to `validate_email`

FAIL
```

✓ Tests fail as expected (unimplemented).

## Step 4: Implement Minimal Code (GREEN)

```cpp
// validator/email.cpp
#include "email.hpp"
#include <regex>

std::expected<void, EmailError> validate_email(const std::string& email) {
    if (email.empty()) {
        return std::unexpected(EmailError::Empty);
    }
    static const std::regex pattern(R"([a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,})");
    if (!std::regex_match(email, pattern)) {
        return std::unexpected(EmailError::InvalidFormat);
    }
    return {};
}
```

## Step 5: Run Tests - Verify PASS

```bash
$ cmake --build build && ctest --test-dir build --output-on-failure

1/1 Test #1: email_validator_test .....   Passed    0.01 sec

100% tests passed.
```

✓ All tests passing!

## Step 6: Check Coverage

```bash
$ cmake -DCMAKE_CXX_FLAGS="--coverage" -B build && cmake --build build
$ ctest --test-dir build
$ lcov --capture --directory build --output-file coverage.info
$ lcov --list coverage.info

validator/email.cpp     | 100%
```

✓ Coverage: 100%

## TDD Complete!
````

## Test Patterns
## 测试模式

### Basic Tests
### 基本测试

```cpp
TEST(SuiteName, TestName) {
    EXPECT_EQ(add(2, 3), 5);
    EXPECT_NE(result, nullptr);
    EXPECT_TRUE(is_valid);
    EXPECT_THROW(func(), std::invalid_argument);
}
```

### Fixtures
### 固件

```cpp
class DatabaseTest : public ::testing::Test {
protected:
    void SetUp() override { db_ = create_test_db(); }
    void TearDown() override { db_.reset(); }
    std::unique_ptr<Database> db_;
};

TEST_F(DatabaseTest, InsertsRecord) {
    db_->insert("key", "value");
    EXPECT_EQ(db_->get("key"), "value");
}
```

### Parameterized Tests
### 参数化测试

```cpp
class PrimeTest : public ::testing::TestWithParam<std::pair<int, bool>> {};

TEST_P(PrimeTest, ChecksPrimality) {
    auto [input, expected] = GetParam();
    EXPECT_EQ(is_prime(input), expected);
}

INSTANTIATE_TEST_SUITE_P(Primes, PrimeTest, ::testing::Values(
    std::make_pair(2, true),
    std::make_pair(4, false),
    std::make_pair(7, true)
));
```

## Coverage Commands
## 覆盖率命令

```bash
# Build with coverage
# 用覆盖率构建
cmake -DCMAKE_CXX_FLAGS="--coverage" -DCMAKE_EXE_LINKER_FLAGS="--coverage" -B build

# Run tests
# 运行测试
cmake --build build && ctest --test-dir build

# Generate coverage report
# 生成覆盖率报告
lcov --capture --directory build --output-file coverage.info
lcov --remove coverage.info '/usr/*' --output-file coverage.info
genhtml coverage.info --output-directory coverage_html
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

- Use `EXPECT_*` (continues) over `ASSERT_*` (stops) when appropriate
- 适当使用 `EXPECT_*`（继续）而不是 `ASSERT_*`（停止）

- Test behavior, not implementation details
- 测试行为，而不是实现细节

- Include edge cases (empty, null, max values, boundary conditions)
- 包含边缘情况（空、null、最大值、边界条件）

**DON'T:**
**不应该：**

- Write implementation before tests
- 先实现再写测试

- Skip the RED phase
- 跳过 RED 阶段

- Test private methods directly (test through public API)
- 直接测试私有方法（通过公共 API 测试）

- Use `sleep` in tests
- 在测试中使用 `sleep`

- Ignore flaky tests
- 忽略不稳定测试

## Related Commands
## 相关命令

- `/cpp-build` - Fix build errors
- `/cpp-build` - 修复构建错误

- `/cpp-review` - Review code after implementation
- `/cpp-review` - 实现后审查代码

- `/verify` - Run full verification loop
- `/verify` - 运行完整验证循环

## Related
## 相关

- Skill: `skills/cpp-testing/`
- Skill: `skills/tdd-workflow/`
