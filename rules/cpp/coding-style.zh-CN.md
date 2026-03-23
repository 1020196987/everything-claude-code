---
name: C++ Coding Style
name: C++ 编码风格
description: C++ coding style guidelines
description: C++ 编码风格规范
paths:
  - "**/*.cpp"
  - "**/*.hpp"
  - "**/*.cc"
  - "**/*.hh"
  - "**/*.cxx"
  - "**/*.h"
  - "**/CMakeLists.txt"
---
# C++ Coding Style
# C++ 编码风格

> This file extends [common/coding-style.md](../common/coding-style.md) with C++ specific content.
> 此文件扩展了 [common/coding-style.md](../common/coding-style.md)，包含 C++ 特定内容。

## Modern C++ (C++17/20/23)
## 现代 C++ (C++17/20/23)

- 优先使用**现代 C++ 特性**而非 C 风格结构（Prefer **modern C++ features** over C-style constructs）
- 当类型从上下文明显时使用 `auto`（Use `auto` when the type is obvious from context）
- 使用 `constexpr` 表示编译时常量（Use `constexpr` for compile-time constants）
- 使用结构化绑定：`auto [key, value] = map_entry;`（Use structured bindings: `auto [key, value] = map_entry;`）

## Resource Management
## 资源管理

- **全面使用 RAII** — 不手动 `new`/`delete`（**RAII everywhere** — no manual `new`/`delete`）
- 使用 `std::unique_ptr` 表示独占所有权（Use `std::unique_ptr` for exclusive ownership）
- 仅在真正需要共享所有权时使用 `std::shared_ptr`（Use `std::shared_ptr` only when shared ownership is truly needed）
- 使用 `std::make_unique` / `std::make_shared` 而非原始 `new`（Use `std::make_unique` / `std::make_shared` over raw `new`）

## Naming Conventions
## 命名约定

- 类型/类：`PascalCase`（Types/Classes: `PascalCase`）
- 函数/方法：`snake_case` 或 `camelCase`（遵循项目约定）（Functions/Methods: `snake_case` or `camelCase` (follow project convention)）
- 常量：`kPascalCase` 或 `UPPER_SNAKE_CASE`（Constants: `kPascalCase` or `UPPER_SNAKE_CASE`）
- 命名空间：`lowercase`（Namespaces: `lowercase`）
- 成员变量：`snake_case_`（下划线后缀）或 `m_` 前缀（Member variables: `snake_case_` (trailing underscore) or `m_` prefix）

## Formatting
## 格式化

- 使用 **clang-format** — 无需风格争论（Use **clang-format** — no style debates）
- 提交前运行 `clang-format -i <file>`（Run `clang-format -i <file>` before committing）

## Reference
## 参考

See skill: `cpp-coding-standards` for comprehensive C++ coding standards and guidelines.
参见 skill: `cpp-coding-standards` 了解全面的 C++ 编码标准和指南。
