---
name: C++ Patterns
name: C++ 模式
description: C++ design patterns and idiomatic syntax
description: C++ 设计模式和惯用语法
paths:
  - "**/*.cpp"
  - "**/*.hpp"
  - "**/*.cc"
  - "**/*.hh"
  - "**/*.cxx"
  - "**/*.h"
  - "**/CMakeLists.txt"
---
# C++ Patterns
# C++ 模式

> This file extends [common/patterns.md](../common/patterns.md) with C++ specific content.
> 此文件扩展了 [common/patterns.md](../common/patterns.md)，包含 C++ 特定内容。

## RAII (Resource Acquisition Is Initialization)
## RAII（资源获取即初始化）

Tie resource lifetime to object lifetime:
将资源生命周期与对象生命周期绑定：

```cpp
class FileHandle {
public:
    explicit FileHandle(const std::string& path) : file_(std::fopen(path.c_str(), "r")) {}
    ~FileHandle() { if (file_) std::fclose(file_); }
    FileHandle(const FileHandle&) = delete;
    FileHandle& operator=(const FileHandle&) = delete;
private:
    std::FILE* file_;
};
```

## Rule of Five/Zero
## 五/零规则

- **Rule of Zero**: Prefer classes that need no custom destructor, copy/move constructors, or assignments
  **零规则**：优先选择不需要自定义析构函数、拷贝/移动构造函数或拷贝/移动赋值运算符的类
- **Rule of Five**: If you define any of destructor/copy-ctor/copy-assign/move-ctor/move-assign, define all five
  **五规则**：如果定义了析构函数/拷贝构造函数/拷贝赋值/移动构造函数/移动赋值中的任何一个，则定义全部五个

## Value Semantics
## 值语义

- Pass small/trivial types by value
  按值传递小型/平凡类型
- Pass large types by `const&`
  按 `const&` 传递大型类型
- Return by value (rely on RVO/NRVO)
  按值返回（依赖 RVO/NRVO）
- Use move semantics for sink parameters
  对接收参数使用移动语义

## Error Handling
## 错误处理

- Use exceptions for exceptional conditions
  对异常条件使用异常
- Use `std::optional` for values that may not exist
  对可能不存在的值使用 `std::optional`
- Use `std::expected` (C++23) or result types for expected failures
  对预期失败使用 `std::expected`（C++23）或结果类型

## Reference
## 参考

See skill: `cpp-coding-standards` for comprehensive C++ patterns and anti-patterns.
参见 skill: `cpp-coding-standards` 了解全面的 C++ 模式和反模式。
