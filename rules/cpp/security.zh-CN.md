---
name: C++ Security
name: C++ 安全
description: C++ security guidelines and memory safety
description: C++ 安全指南和内存安全
paths:
  - "**/*.cpp"
  - "**/*.hpp"
  - "**/*.cc"
  - "**/*.hh"
  - "**/*.cxx"
  - "**/*.h"
  - "**/CMakeLists.txt"
---
# C++ Security
# C++ 安全

> This file extends [common/security.md](../common/security.md) with C++ specific content.
> 此文件扩展了 [common/security.md](../common/security.md)，包含 C++ 特定内容。

## Memory Safety
## 内存安全

- Never use raw `new`/`delete` — use smart pointers
- 永不使用原始 `new`/`delete` — 使用智能指针
- Never use C-style arrays — use `std::array` or `std::vector`
- 永不使用 C 风格数组 — 使用 `std::array` 或 `std::vector`
- Never use `malloc`/`free` — use C++ allocation
- 永不使用 `malloc`/`free` — 使用 C++ 分配
- Avoid `reinterpret_cast` unless absolutely necessary
- 避免 `reinterpret_cast`，除非绝对必要

## Buffer Overflows
## 缓冲区溢出

- Use `std::string` over `char*`
- 使用 `std::string` 而非 `char*`
- Use `.at()` for bounds-checked access when safety matters
- 在安全性重要时使用 `.at()` 进行边界检查访问
- Never use `strcpy`, `strcat`, `sprintf` — use `std::string` or `fmt::format`
- 永不使用 `strcpy`、`strcat`、`sprintf` — 使用 `std::string` 或 `fmt::format`

## Undefined Behavior
## 未定义行为

- Always initialize variables
- 始终初始化变量
- Avoid signed integer overflow
- 避免有符号整数溢出
- Never dereference null or dangling pointers
- 永不解引用空指针或悬空指针
- Use sanitizers in CI:
- 在 CI 中使用 sanitizers：
  ```bash
  cmake -DCMAKE_CXX_FLAGS="-fsanitize=address,undefined" ..
  ```

## Static Analysis
## 静态分析

- Use **clang-tidy** for automated checks:
- 使用 **clang-tidy** 进行自动检查：
  ```bash
  clang-tidy --checks='*' src/*.cpp
  ```
- Use **cppcheck** for additional analysis:
- 使用 **cppcheck** 进行额外分析：
  ```bash
  cppcheck --enable=all src/
  ```

## Reference
## 参考

See skill: `cpp-coding-standards` for detailed security guidelines.
参见 skill: `cpp-coding-standards` 了解详细的安全指南。
