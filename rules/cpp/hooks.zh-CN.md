---
name: C++ Hooks
name: C++ 钩子
description: C++ build hook configuration
description: C++ 构建钩子配置
paths:
  - "**/*.cpp"
  - "**/*.hpp"
  - "**/*.cc"
  - "**/*.hh"
  - "**/*.cxx"
  - "**/*.h"
  - "**/CMakeLists.txt"
---
# C++ Hooks
# C++ 钩子

> This file extends [common/hooks.md](../common/hooks.md) with C++ specific content.
> 此文件扩展了 [common/hooks.md](../common/hooks.md)，包含 C++ 特定内容。

## Build Hooks
## 构建钩子

Run these checks before committing C++ changes:
提交 C++ 更改前运行这些检查：

```bash
# Format check
# 格式检查
clang-format --dry-run --Werror src/*.cpp src/*.hpp

# Static analysis
# 静态分析
clang-tidy src/*.cpp -- -std=c++17

# Build
# 构建
cmake --build build

# Tests
# 测试
ctest --test-dir build --output-on-failure
```

## Recommended CI Pipeline
## 推荐的 CI 流水线

1. **clang-format** — formatting check
1. **clang-format** — 格式检查
2. **clang-tidy** — static analysis
2. **clang-tidy** — 静态分析
3. **cppcheck** — additional analysis
3. **cppcheck** — 额外分析
4. **cmake build** — compilation
4. **cmake build** — 编译
5. **ctest** — test execution with sanitizers
5. **ctest** — 使用 sanitizers 执行测试
