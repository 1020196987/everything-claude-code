---
name: swift-protocol-di-testing
description: Protocol-based dependency injection for testable Swift code — mock file system, network, and external APIs using focused protocols and Swift Testing.
description zh-CN: 基于协议的依赖注入实现可测试的 Swift 代码 — 使用专注的协议和 Swift Testing 模拟文件系统、网络和外部 API。
origin: ECC
---

# Swift Protocol-Based Dependency Injection for Testing
# Swift 基于协议的依赖注入用于测试

Patterns for making Swift code testable by abstracting external dependencies (file system, network, iCloud) behind small, focused protocols. Enables deterministic tests without I/O.
通过将外部依赖（文件系统、网络、iCloud）抽象到小的、专注的协议后面，使 Swift 代码可测试的模式。实现无需 I/O 的确定性测试。

## When to Activate
## 何时激活

- Writing Swift code that accesses file system, network, or external APIs
- 编写访问文件系统、网络或外部 API 的 Swift 代码
- Need to test error handling paths without triggering real failures
- 需要测试错误处理路径而不触发真实失败
- Building modules that work across environments (app, test, SwiftUI preview)
- 构建跨环境工作的模块（应用、测试、SwiftUI 预览）
- Designing testable architecture with Swift concurrency (actors, Sendable)
- 使用 Swift 并发（actors、Sendable）设计可测试架构

## Core Pattern
## 核心模式

### 1. Define Small, Focused Protocols
### 1. 定义小的、专注的协议

Each protocol handles exactly one external concern.
每个协议处理一个外部关注点。

```swift
// File system access
public protocol FileSystemProviding: Sendable {
    func containerURL(for purpose: Purpose) -> URL?
}

// File read/write operations
public protocol FileAccessorProviding: Sendable {
    func read(from url: URL) throws -> Data
    func write(_ data: Data, to url: URL) throws
    func fileExists(at url: URL) -> Bool
}

// Bookmark storage (e.g., for sandboxed apps)
public protocol BookmarkStorageProviding: Sendable {
    func saveBookmark(_ data: Data, for key: String) throws
    func loadBookmark(for key: String) throws -> Data?
}
```

### 2. Create Default (Production) Implementations
### 2. 创建默认（生产）实现

```swift
public struct DefaultFileSystemProvider: FileSystemProviding {
    public init() {}

    public func containerURL(for purpose: Purpose) -> URL? {
        FileManager.default.url(forUbiquityContainerIdentifier: nil)
    }
}

public struct DefaultFileAccessor: FileAccessorProviding {
    public init() {}

    public func read(from url: URL) throws -> Data {
        try Data(contentsOf: url)
    }

    public func write(_ data: Data, to url: URL) throws {
        try data.write(to: url, options: .atomic)
    }

    public func fileExists(at url: URL) -> Bool {
        FileManager.default.fileExists(atPath: url.path)
    }
}
```

### 3. Create Mock Implementations for Testing
### 3. 为测试创建 Mock 实现

```swift
public final class MockFileAccessor: FileAccessorProviding, @unchecked Sendable {
    public var files: [URL: Data] = [:]
    public var readError: Error?
    public var writeError: Error?

    public init() {}

    public func read(from url: URL) throws -> Data {
        if let error = readError { throw error }
        guard let data = files[url] else {
            throw CocoaError(.fileReadNoSuchFile)
        }
        return data
    }

    public func write(_ data: Data, to url: URL) throws {
        if let error = writeError { throw error }
        files[url] = data
    }

    public func fileExists(at url: URL) -> Bool {
        files[url] != nil
    }
}
```

### 4. Inject Dependencies with Default Parameters
### 4. 用默认参数注入依赖

Production code uses defaults; tests inject mocks.
生产代码使用默认值；测试注入 mocks。

```swift
public actor SyncManager {
    private let fileSystem: FileSystemProviding
    private let fileAccessor: FileAccessorProviding

    public init(
        fileSystem: FileSystemProviding = DefaultFileSystemProvider(),
        fileAccessor: FileAccessorProviding = DefaultFileAccessor()
    ) {
        self.fileSystem = fileSystem
        self.fileAccessor = fileAccessor
    }

    public func sync() async throws {
        guard let containerURL = fileSystem.containerURL(for: .sync) else {
            throw SyncError.containerNotAvailable
        }
        let data = try fileAccessor.read(
            from: containerURL.appendingPathComponent("data.json")
        )
        // Process data...
    }
}
```

### 5. Write Tests with Swift Testing
### 5. 用 Swift Testing 编写测试

```swift
import Testing

@Test("Sync manager handles missing container")
func testMissingContainer() async {
    let mockFileSystem = MockFileSystemProvider(containerURL: nil)
    let manager = SyncManager(fileSystem: mockFileSystem)

    await #expect(throws: SyncError.containerNotAvailable) {
        try await manager.sync()
    }
}

@Test("Sync manager reads data correctly")
func testReadData() async throws {
    let mockFileAccessor = MockFileAccessor()
    mockFileAccessor.files[testURL] = testData

    let manager = SyncManager(fileAccessor: mockFileAccessor)
    let result = try await manager.loadData()

    #expect(result == expectedData)
}

@Test("Sync manager handles read errors gracefully")
func testReadError() async {
    let mockFileAccessor = MockFileAccessor()
    mockFileAccessor.readError = CocoaError(.fileReadCorruptFile)

    let manager = SyncManager(fileAccessor: mockFileAccessor)

    await #expect(throws: SyncError.self) {
        try await manager.sync()
    }
}
```

## Best Practices
## 最佳实践

- **Single Responsibility**: Each protocol should handle one concern — don't create "god protocols" with many methods
- **单一职责**：每个协议应处理一个关注点 — 不要创建有很多方法的"上帝协议"
- **Sendable conformance**: Required when protocols are used across actor boundaries
- **Sendable 遵循**：当协议跨 actor 边界使用时是必需的
- **Default parameters**: Let production code use real implementations by default; only tests need to specify mocks
- **默认参数**：让生产代码默认使用真实实现；只有测试需要指定 mocks
- **Error simulation**: Design mocks with configurable error properties for testing failure paths
- **错误模拟**：设计带有可配置错误属性的 mocks 以测试失败路径
- **Only mock boundaries**: Mock external dependencies (file system, network, APIs), not internal types
- **仅模拟边界**：模拟外部依赖（文件系统、网络、API），而非内部类型

## Anti-Patterns to Avoid
## 应避免的反模式

- Creating a single large protocol that covers all external access
- 创建覆盖所有外部访问的单个大协议
- Mocking internal types that have no external dependencies
- 模拟没有外部依赖的内部类型
- Using `#if DEBUG` conditionals instead of proper dependency injection
- 使用 `#if DEBUG` 条件而不是适当的依赖注入
- Forgetting `Sendable` conformance when used with actors
- 与 actors 一起使用时忘记 `Sendable` 遵循
- Over-engineering: if a type has no external dependencies, it doesn't need a protocol
- 过度工程：如果一个类型没有外部依赖，它不需要协议

## When to Use
## 何时使用

- Any Swift code that touches file system, network, or external APIs
- 任何接触文件系统、网络或外部 API 的 Swift 代码
- Testing error handling paths that are hard to trigger in real environments
- 测试在真实环境中难以触发的错误处理路径
- Building modules that need to work in app, test, and SwiftUI preview contexts
- 构建需要在应用、测试和 SwiftUI 预览上下文中工作的模块
- Apps using Swift concurrency (actors, structured concurrency) that need testable architecture
- 需要可测试架构的使用 Swift 并发（actors、结构化并发）的应用
