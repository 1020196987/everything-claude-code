---
name: swift-coding-style
description: Swift coding style guidelines
description: Swift 编码风格规范
paths:
  - "**/*.swift"
  - "**/Package.swift"
---

# Swift Coding Style
# Swift 编码风格

> This file extends [common/coding-style.md](../common/coding-style.md) with Swift specific content.
> 此文件扩展了 [common/coding-style.md](../common/coding-style.md)，包含 Swift 特定内容。

## Formatting
## 格式化

- **SwiftFormat** for auto-formatting
  **SwiftFormat** 用于自动格式化
- **SwiftLint** for style enforcement
  **SwiftLint** 用于风格强制
- `swift-format` is bundled with Xcode 16+ as an alternative
  `swift-format` 作为替代方案已绑定到 Xcode 16+

## Immutability
## 不可变性

- Prefer `let` over `var` — define everything as `let` and only change to `var` if the compiler requires it
  优先使用 `let` 而非 `var` — 将所有内容定义为 `let`，只有在编译器要求时才改为 `var`
- Use `struct` with value semantics by default; use `class` only when identity or reference semantics are needed
  默认使用值语义的 `struct`；仅在需要标识或引用语义时才使用 `class`

## Naming
## 命名

Follow [Apple API Design Guidelines](https://www.swift.org/documentation/api-design-guidelines/):
遵循 [Apple API 设计指南](https://www.swift.org/documentation/api-design-guidelines/)：

- Clarity at the point of use — omit needless words
  在使用点清晰 — 省略不必要的词
- Name methods and properties for their roles, not their types
  根据角色命名方法和属性，而非根据类型
- Use `static let` for constants over global constants
  对常量使用 `static let` 而非全局常量

## Error Handling
## 错误处理

Use typed throws (Swift 6+) and pattern matching:
使用类型化 throws（Swift 6+）和模式匹配：

```swift
func load(id: String) throws(LoadError) -> Item {
    guard let data = try? read(from: path) else {
        throw .fileNotFound(id)
    }
    return try decode(data)
}
```

## Concurrency
## 并发

Enable Swift 6 strict concurrency checking. Prefer:
启用 Swift 6 严格并发检查。优先使用：

- `Sendable` value types for data crossing isolation boundaries
  跨隔离边界的数据使用 `Sendable` 值类型
- Actors for shared mutable state
  共享可变状态使用 Actor
- Structured concurrency (`async let`, `TaskGroup`) over unstructured `Task {}`
  结构化并发（`async let`、`TaskGroup`）而非非结构化 `Task {}`
