---
name: C++ Testing
name: C++ 测试
description: C++ testing framework and coverage
description: C++ 测试框架和覆盖率
paths:
  - "**/*.cpp"
  - "**/*.hpp"
  - "**/*.cc"
  - "**/*.hh"
  - "**/*.cxx"
  - "**/*.h"
  - "**/CMakeLists.txt"
---
# C++ Testing
# C++ 测试

> This file extends [common/testing.md](../common/testing.md) with C++ specific content.
> 此文件扩展了 [common/testing.md](../common/testing.md)，包含 C++ 特定内容。

## Framework
## 框架

Use **GoogleTest** (gtest/gmock) with **CMake/CTest**.
使用 **GoogleTest**（gtest/gmock）配合 **CMake/CTest**。

## Running Tests
## 运行测试

```bash
cmake --build build && ctest --test-dir build --output-on-failure
```

## Coverage
## 覆盖率

```bash
cmake -DCMAKE_CXX_FLAGS="--coverage" -DCMAKE_EXE_LINKER_FLAGS="--coverage" ..
cmake --build .
ctest --output-on-failure
lcov --capture --directory . --output-file coverage.info
```

## Sanitizers
## Sanitizers

Always run tests with sanitizers in CI:
在 CI 中始终使用 sanitizers 运行测试：

```bash
cmake -DCMAKE_CXX_FLAGS="-fsanitize=address,undefined" ..
```

## Reference
## 参考

See skill: `cpp-testing` for detailed C++ testing patterns, TDD workflow, and GoogleTest/GMock usage.
参见 skill: `cpp-testing` 了解详细的 C++ 测试模式、TDD 工作流以及 GoogleTest/GMock 的使用。
