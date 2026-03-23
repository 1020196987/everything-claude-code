---
name: swift-testing
description: Swift testing framework and patterns
description: Swift 测试框架和模式
paths:
  - "**/*.swift"
  - "**/Package.swift"
---

# Swift Testing
# Swift 测试

> This file extends [common/testing.md](../common/testing.md) with Swift specific content.
> 此文件扩展了 [common/testing.md](../common/testing.md)，包含 Swift 特定内容。

## Framework
## 框架

Use **Swift Testing** (`import Testing`) for new tests. Use `@Test` and `#expect`:
对新测试使用 **Swift Testing**（`import Testing`）。使用 `@Test` 和 `#expect`：

```swift
@Test("User creation validates email")
func userCreationValidatesEmail() throws {
    #expect(throws: ValidationError.invalidEmail) {
        try User(email: "not-an-email")
    }
}
```

## Test Isolation
## 测试隔离

Each test gets a fresh instance — set up in `init`, tear down in `deinit`. No shared mutable state between tests.
每个测试获得一个新实例 — 在 `init` 中设置，在 `deinit` 中清理。测试之间无共享可变状态。

## Parameterized Tests
## 参数化测试

```swift
@Test("Validates formats", arguments: ["json", "xml", "csv"])
func validatesFormat(format: String) throws {
    let parser = try Parser(format: format)
    #expect(parser.isValid)
}
```

## Coverage
## 覆盖率

```bash
swift test --enable-code-coverage
```

## Reference
## 参考

See skill: `swift-protocol-di-testing` for protocol-based dependency injection and mock patterns with Swift Testing.
参见 skill: `swift-protocol-di-testing` 了解基于协议的依赖注入和 Swift Testing 的 mock 模式。
