---
name: coding-style
description: Go coding style and idioms
description: Go 编码风格和惯用法
---

# Coding Style
# Go 编码风格

> This file extends [common/coding-style.md](../common/coding-style.md) with Go specific content.
> 此文件扩展了 [common/coding-style.md](../common/coding-style.md)，包含 Go 特定内容。

## Formatting
## 格式化

- **gofmt** 和 **goimports** 是强制的 — 无需风格争论（**gofmt** and **goimports** are mandatory — no style debates）

## Design Principles
## 设计原则

- 接收接口，返回结构体（Accept interfaces, return structs）
- 保持接口小巧（1-3 个方法）（Keep interfaces small (1-3 methods)）

## Error Handling
## 错误处理

Always wrap errors with context:
始终用上下文包装错误：

```go
if err != nil {
    return fmt.Errorf("failed to create user: %w", err)
}
```

## Reference
## 参考

See skill: `golang-patterns` for comprehensive Go idioms and patterns.
参见 skill: `golang-patterns` 了解全面的 Go 惯用法和模式。
