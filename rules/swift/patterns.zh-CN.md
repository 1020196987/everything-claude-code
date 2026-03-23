---
name: swift-patterns
description: Swift design patterns and protocol-oriented programming
description: Swift 设计模式和协议导向编程
paths:
  - "**/*.swift"
  - "**/Package.swift"
---

# Swift Patterns
# Swift 模式

> This file extends [common/patterns.md](../common/patterns.md) with Swift specific content.
> 此文件扩展了 [common/patterns.md](../common/patterns.md)，包含 Swift 特定内容。

## Protocol-Oriented Design
## 协议导向设计

Define small, focused protocols. Use protocol extensions for shared defaults:
定义小而专注的协议。使用协议扩展提供共享默认实现：

```swift
protocol Repository: Sendable {
    associatedtype Item: Identifiable & Sendable
    func find(by id: Item.ID) async throws -> Item?
    func save(_ item: Item) async throws
}
```

## Value Types
## 值类型

- Use structs for data transfer objects and models
- 使用结构体作为数据传输对象和模型
- Use enums with associated values to model distinct states:
- 使用带关联值的枚举来建模不同的状态：

```swift
enum LoadState<T: Sendable>: Sendable {
    case idle
    case loading
    case loaded(T)
    case failed(Error)
}
```

## Actor Pattern
## Actor 模式

Use actors for shared mutable state instead of locks or dispatch queues:
使用 actor 处理共享可变状态，而非锁或调度队列：

```swift
actor Cache<Key: Hashable & Sendable, Value: Sendable> {
    private var storage: [Key: Value] = [:]

    func get(_ key: Key) -> Value? { storage[key] }
    func set(_ key: Key, value: Value) { storage[key] = value }
}
```

## Dependency Injection
## 依赖注入

Inject protocols with default parameters — production uses defaults, tests inject mocks:
使用默认参数注入协议 — 生产环境使用默认实现，测试注入 mock：

```swift
struct UserService {
    private let repository: any UserRepository

    init(repository: any UserRepository = DefaultUserRepository()) {
        self.repository = repository
    }
}
```

## References
## 参考

See skill: `swift-actor-persistence` for actor-based persistence patterns.
参见 skill: `swift-actor-persistence` 了解基于 actor 的持久化模式。
See skill: `swift-protocol-di-testing` for protocol-based DI and testing.
参见 skill: `swift-protocol-di-testing` 了解基于协议的 DI 和测试。
