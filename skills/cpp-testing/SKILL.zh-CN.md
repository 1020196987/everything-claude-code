---
name: cpp-testing
description: Use only when writing/updating/fixing C++ tests, configuring GoogleTest/CTest, diagnosing failing or flaky tests, or adding coverage/sanitizers.
description zh-CN: 仅在编写/更新/修复 C++ 测试、配置 GoogleTest/CTest、诊断失败或不稳定测试、或添加覆盖率/消毒器时使用。
origin: ECC
---

# C++ Testing (Agent Skill)
# C++ Testing (Agent Skill)
# C++ 测试（Agent 技能）

Agent-focused testing workflow for modern C++ (C++17/20) using GoogleTest/GoogleMock with CMake/CTest.
## Agent-focused testing workflow for modern C++ (C++17/20) using GoogleTest/GoogleMock with CMake/CTest.
## 使用 GoogleTest/GoogleMock 和 CMake/CTest 的现代 C++（C++17/20）的 agent 专注测试工作流。

## When to Use
## When to Use
## 何时使用

- Writing new C++ tests or fixing existing tests
  - 编写新的 C++ 测试或修复现有测试
- Designing unit/integration test coverage for C++ components
  - 为 C++ 组件设计单元/集成测试覆盖
- Adding test coverage, CI gating, or regression protection
  - 添加测试覆盖、CI 门控或回归保护
- Configuring CMake/CTest workflows for consistent execution
  - 配置 CMake/CTest 工作流以实现一致执行
- Investigating test failures or flaky behavior
  - 调查测试失败或不稳定行为
- Enabling sanitizers for memory/race diagnostics
  - 启用消毒器以进行内存/竞争诊断

### When NOT to Use
### When NOT to Use
### 何时不使用

- Implementing new product features without test changes
  - 实现新的产品功能但不改变测试
- Large-scale refactors unrelated to test coverage or failures
  - 与测试覆盖或失败无关的大规模重构
- Performance tuning without test regressions to validate
  - 在没有测试回归需要验证的情况下进行性能调优
- Non-C++ projects or non-test tasks
  - 非 C++ 项目或非测试任务

## Core Concepts
## Core Concepts
## 核心概念

- **TDD loop**: red -> green -> refactor (tests first, minimal fix, then cleanups).
  - **TDD 循环**：red -> green -> refactor（先测试、最小修复、然后清理）。
- **Isolation**: prefer dependency injection and fakes over global state.
  - **隔离**：优先使用依赖注入和 fake，而非全局状态。
- **Test layout**: `tests/unit`, `tests/integration`, `tests/testdata`.
  - **测试布局**：`tests/unit`、`tests/integration`、`tests/testdata`。
- **Mocks vs fakes**: mock for interactions, fake for stateful behavior.
  - **Mock vs Fake**：mock 用于交互，fake 用于有状态行为。
- **CTest discovery**: use `gtest_discover_tests()` for stable test discovery.
  - **CTest 发现**：使用 `gtest_discover_tests()` 以实现稳定的测试发现。
- **CI signal**: run subset first, then full suite with `--output-on-failure`.
  - **CI 信号**：先运行子集，然后使用 `--output-on-failure` 运行完整套件。

## TDD Workflow
## TDD Workflow
## TDD 工作流

Follow the RED -> GREEN -> REFACTOR loop:
## Follow the RED -> GREEN -> REFACTOR loop.
## 遵循 RED -> GREEN -> REFACTOR 循环：

1. **RED**: write a failing test that captures the new behavior
   - **RED**：编写一个捕获新行为的失败测试
2. **GREEN**: implement the smallest change to pass
   - **GREEN**：实现最小的更改以通过
3. **REFACTOR**: clean up while tests stay green
   - **REFACTOR**：在测试保持绿色时清理

```cpp
// tests/add_test.cpp
#include <gtest/gtest.h>

int Add(int a, int b); // Provided by production code.

TEST(AddTest, AddsTwoNumbers) { // RED
  EXPECT_EQ(Add(2, 3), 5);
}

// src/add.cpp
int Add(int a, int b) { // GREEN
  return a + b;
}

// REFACTOR: simplify/rename once tests pass
```

## Code Examples
## Code Examples
## 代码示例

### Basic Unit Test (gtest)
### Basic Unit Test (gtest)
### 基本单元测试（gtest）

```cpp
// tests/calculator_test.cpp
#include <gtest/gtest.h>

int Add(int a, int b); // Provided by production code.

TEST(CalculatorTest, AddsTwoNumbers) {
    EXPECT_EQ(Add(2, 3), 5);
}
```

### Fixture (gtest)
### Fixture (gtest)
### 测试夹具（gtest）

```cpp
// tests/user_store_test.cpp
// Pseudocode stub: replace UserStore/User with project types.
#include <gtest/gtest.h>
#include <memory>
#include <optional>
#include <string>

struct User { std::string name; };
class UserStore {
public:
    explicit UserStore(std::string /*path*/) {}
    void Seed(std::initializer_list<User> /*users*/) {}
    std::optional<User> Find(const std::string &/*name*/) { return User{"alice"}; }
};

class UserStoreTest : public ::testing::Test {
protected:
    void SetUp() override {
        store = std::make_unique<UserStore>(":memory:");
        store->Seed({{"alice"}, {"bob"}});
    }

    std::unique_ptr<UserStore> store;
};

TEST_F(UserStoreTest, FindsExistingUser) {
    auto user = store->Find("alice");
    ASSERT_TRUE(user.has_value());
    EXPECT_EQ(user->name, "alice");
}
```

### Mock (gmock)
### Mock (gmock)
### Mock（gmock）

```cpp
// tests/notifier_test.cpp
#include <gmock/gmock.h>
#include <gtest/gtest.h>
#include <string>

class Notifier {
public:
    virtual ~Notifier() = default;
    virtual void Send(const std::string &message) = 0;
};

class MockNotifier : public Notifier {
public:
    MOCK_METHOD(void, Send, (const std::string &message), (override));
};

class Service {
public:
    explicit Service(Notifier &notifier) : notifier_(notifier) {}
    void Publish(const std::string &message) { notifier_.Send(message); }

private:
    Notifier &notifier_;
};

TEST(ServiceTest, SendsNotifications) {
    MockNotifier notifier;
    Service service(notifier);

    EXPECT_CALL(notifier, Send("hello")).Times(1);
    service.Publish("hello");
}
```

### CMake/CTest Quickstart
### CMake/CTest Quickstart
### CMake/CTest 快速入门

```cmake
# CMakeLists.txt (excerpt)
cmake_minimum_required(VERSION 3.20)
project(example LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

include(FetchContent)
# Prefer project-locked versions. If using a tag, use a pinned version per project policy.
set(GTEST_VERSION v1.17.0) # Adjust to project policy.
FetchContent_Declare(
  googletest
  # Google Test framework (official repository)
  URL https://github.com/google/googletest/archive/refs/tags/${GTEST_VERSION}.zip
)
FetchContent_MakeAvailable(googletest)

add_executable(example_tests
  tests/calculator_test.cpp
  src/calculator.cpp
)
target_link_libraries(example_tests GTest::gtest GTest::gmock GTest::gtest_main)

enable_testing()
include(GoogleTest)
gtest_discover_tests(example_tests)
```

```bash
cmake -S . -B build -DCMAKE_BUILD_TYPE=Debug
cmake --build build -j
ctest --test-dir build --output-on-failure
```

## Running Tests
## Running Tests
## 运行测试

```bash
ctest --test-dir build --output-on-failure
ctest --test-dir build -R ClampTest
ctest --test-dir build -R "UserStoreTest.*" --output-on-failure
```

```bash
./build/example_tests --gtest_filter=ClampTest.*
./build/example_tests --gtest_filter=UserStoreTest.FindsExistingUser
```

## Debugging Failures
## Debugging Failures
## 调试失败

1. Re-run the single failing test with gtest filter.
   - 使用 gtest 过滤器重新运行单个失败测试。
2. Add scoped logging around the failing assertion.
   - 在失败断言周围添加作用域日志。
3. Re-run with sanitizers enabled.
   - 启用消毒器后重新运行。
4. Expand to full suite once the root cause is fixed.
   - 修复根本原因后扩展到完整套件。

## Coverage
## Coverage
## 覆盖率

Prefer target-level settings instead of global flags.
## Prefer target-level settings instead of global flags.
## 优先使用目标级设置而非全局标志。

```cmake
option(ENABLE_COVERAGE "Enable coverage flags" OFF)

if(ENABLE_COVERAGE)
  if(CMAKE_CXX_COMPILER_ID MATCHES "GNU")
    target_compile_options(example_tests PRIVATE --coverage)
    target_link_options(example_tests PRIVATE --coverage)
  elseif(CMAKE_CXX_COMPILER_ID MATCHES "Clang")
    target_compile_options(example_tests PRIVATE -fprofile-instr-generate -fcoverage-mapping)
    target_link_options(example_tests PRIVATE -fprofile-instr-generate)
  endif()
endif()
```

GCC + gcov + lcov:
## GCC + gcov + lcov:

```bash
cmake -S . -B build-cov -DENABLE_COVERAGE=ON
cmake --build build-cov -j
ctest --test-dir build-cov
lcov --capture --directory build-cov --output-file coverage.info
lcov --remove coverage.info '/usr/*' --output-file coverage.info
genhtml coverage.info --output-directory coverage
```

Clang + llvm-cov:
## Clang + llvm-cov:

```bash
cmake -S . -B build-llvm -DENABLE_COVERAGE=ON -DCMAKE_CXX_COMPILER=clang++
cmake --build build-llvm -j
LLVM_PROFILE_FILE="build-llvm/default.profraw" ctest --test-dir build-llvm
llvm-profdata merge -sparse build-llvm/default.profraw -o build-llvm/default.profdata
llvm-cov report build-llvm/example_tests -instr-profile=build-llvm/default.profdata
```

## Sanitizers
## Sanitizers
## 消毒器

```cmake
option(ENABLE_ASAN "Enable AddressSanitizer" OFF)
option(ENABLE_UBSAN "Enable UndefinedBehaviorSanitizer" OFF)
option(ENABLE_TSAN "Enable ThreadSanitizer" OFF)

if(ENABLE_ASAN)
  add_compile_options(-fsanitize=address -fno-omit-frame-pointer)
  add_link_options(-fsanitize=address)
endif()
if(ENABLE_UBSAN)
  add_compile_options(-fsanitize=undefined -fno-omit-frame-pointer)
  add_link_options(-fsanitize=undefined)
endif()
if(ENABLE_TSAN)
  add_compile_options(-fsanitize=thread)
  add_link_options(-fsanitize=thread)
endif()
```

## Flaky Tests Guardrails
## Flaky Tests Guardrails
## 不稳定测试护栏

- Never use `sleep` for synchronization; use condition variables or latches.
  - 绝不要使用 `sleep` 进行同步；使用条件变量或闩锁。
- Make temp directories unique per test and always clean them.
  - 为每个测试创建唯一的临时目录并始终清理它们。
- Avoid real time, network, or filesystem dependencies in unit tests.
  - 在单元测试中避免真实时间、网络或文件系统依赖。
- Use deterministic seeds for randomized inputs.
  - 对随机输入使用确定性种子。

## Best Practices
## Best Practices
## 最佳实践

### DO
### DO
### 应该做

- Keep tests deterministic and isolated
  - 保持测试确定性和隔离性
- Prefer dependency injection over globals
  - 优先使用依赖注入而非全局变量
- Use `ASSERT_*` for preconditions, `EXPECT_*` for multiple checks
  - 对前置条件使用 `ASSERT_*`，对多个检查使用 `EXPECT_*`
- Separate unit vs integration tests in CTest labels or directories
  - 在 CTest 标签或目录中分离单元测试和集成测试
- Run sanitizers in CI for memory and race detection
  - 在 CI 中运行消毒器以检测内存和竞争

### DON'T
### DON'T
### 不应该做

- Don't depend on real time or network in unit tests
  - 不要在单元测试中依赖真实时间或网络
- Don't use sleeps as synchronization when a condition variable can be used
  - 当可以使用条件变量时，不要使用 sleep 作为同步
- Don't over-mock simple value objects
  - 不要过度 mock 简单值对象
- Don't use brittle string matching for non-critical logs
  - 不要对非关键日志使用脆弱的字符串匹配

### Common Pitfalls
### Common Pitfalls
### 常见陷阱

- **Using fixed temp paths** -> Generate unique temp directories per test and clean them.
  - **使用固定临时路径** -> 为每个测试生成唯一的临时目录并清理它们。
- **Relying on wall clock time** -> Inject a clock or use fake time sources.
  - **依赖挂钟时间** -> 注入时钟或使用假时间源。
- **Flaky concurrency tests** -> Use condition variables/latches and bounded waits.
  - **不稳定并发测试** -> 使用条件变量/闩锁和有界等待。
- **Hidden global state** -> Reset global state in fixtures or remove globals.
  - **隐藏的全局状态** -> 在夹具中重置全局状态或移除全局变量。
- **Over-mocking** -> Prefer fakes for stateful behavior and only mock interactions.
  - **过度 mock** -> 对有状态行为优先使用 fake，仅 mock 交互。
- **Missing sanitizer runs** -> Add ASan/UBSan/TSan builds in CI.
  - **缺少消毒器运行** -> 在 CI 中添加 ASan/UBSan/TSan 构建。
- **Coverage on debug-only builds** -> Ensure coverage targets use consistent flags.
  - **仅在调试版本上覆盖** -> 确保覆盖率目标使用一致的标志。

## Optional Appendix: Fuzzing / Property Testing
## Optional Appendix: Fuzzing / Property Testing
## 可选附录：模糊测试/属性测试

Only use if the project already supports LLVM/libFuzzer or a property-testing library.
## Only use if the project already supports LLVM/libFuzzer or a property-testing library.
## 仅在项目已支持 LLVM/libFuzzer 或属性测试库时使用。

- **libFuzzer**: best for pure functions with minimal I/O.
  - **libFuzzer**：最适合几乎没有 I/O 的纯函数。
- **RapidCheck**: property-based tests to validate invariants.
  - **RapidCheck**：验证不变量的属性测试。

Minimal libFuzzer harness (pseudocode: replace ParseConfig):
## Minimal libFuzzer harness (pseudocode: replace ParseConfig).
## 最小 libFuzzer 工具（伪代码：替换 ParseConfig）：

```cpp
#include <cstddef>
#include <cstdint>
#include <string>

extern "C" int LLVMFuzzerTestOneInput(const uint8_t *data, size_t size) {
    std::string input(reinterpret_cast<const char *>(data), size);
    // ParseConfig(input); // project function
    return 0;
}
```

## Alternatives to GoogleTest
## Alternatives to GoogleTest
## GoogleTest 的替代品

- **Catch2**: header-only, expressive matchers
  - **Catch2**：仅头文件，富有表现力的匹配器
- **doctest**: lightweight, minimal compile overhead
  - **doctest**：轻量级，最小编译开销
