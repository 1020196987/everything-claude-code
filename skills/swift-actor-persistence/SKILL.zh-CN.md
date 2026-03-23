---
name: swift-actor-persistence
description: Thread-safe data persistence in Swift using actors — in-memory cache with file-backed storage, eliminating data races by design.
description zh-CN: 使用 Swift actors 实现线程安全的数据持久化 — 带文件支持的内存缓存，通过设计消除数据竞争。
origin: ECC
---

# Swift Actors for Thread-Safe Persistence
# Swift Actors 线程安全持久化

Patterns for building thread-safe data persistence layers using Swift actors. Combines in-memory caching with file-backed storage, leveraging the actor model to eliminate data races at compile time.
使用 Swift actors 构建线程安全数据持久化层的模式。结合内存缓存与文件支持存储，利用 actor 模型在编译时消除数据竞争。

## When to Activate
## 何时激活

- Building a data persistence layer in Swift 5.5+
- 在 Swift 5.5+ 中构建数据持久化层
- Need thread-safe access to shared mutable state
- 需要对共享可变状态的线程安全访问
- Want to eliminate manual synchronization (locks, DispatchQueues)
- 想要消除手动同步（锁、DispatchQueues）
- Building offline-first apps with local storage
- 构建带有本地存储的离线优先应用

## Core Pattern
## 核心模式

### Actor-Based Repository
### 基于 Actor 的仓库

The actor model guarantees serialized access — no data races, enforced by the compiler.
actor 模型保证序列化访问 — 无数据竞争，由编译器强制执行。

```swift
public actor LocalRepository<T: Codable & Identifiable> where T.ID == String {
    private var cache: [String: T] = [:]
    private let fileURL: URL

    public init(directory: URL = .documentsDirectory, filename: String = "data.json") {
        self.fileURL = directory.appendingPathComponent(filename)
        // Synchronous load during init (actor isolation not yet active)
        self.cache = Self.loadSynchronously(from: fileURL)
    }

    // MARK: - Public API

    public func save(_ item: T) throws {
        cache[item.id] = item
        try persistToFile()
    }

    public func delete(_ id: String) throws {
        cache[id] = nil
        try persistToFile()
    }

    public func find(by id: String) -> T? {
        cache[id]
    }

    public func loadAll() -> [T] {
        Array(cache.values)
    }

    // MARK: - Private

    private func persistToFile() throws {
        let data = try JSONEncoder().encode(Array(cache.values))
        try data.write(to: fileURL, options: .atomic)
    }

    private static func loadSynchronously(from url: URL) -> [String: T] {
        guard let data = try? Data(contentsOf: url),
              let items = try? JSONDecoder().decode([T].self, from: data) else {
            return [:]
        }
        return Dictionary(uniqueKeysWithValues: items.map { ($0.id, $0) })
    }
}
```

### Usage
### 用法

All calls are automatically async due to actor isolation:
由于 actor 隔离，所有调用自动变为 async：

```swift
let repository = LocalRepository<Question>()

// Read — fast O(1) lookup from in-memory cache
let question = await repository.find(by: "q-001")
let allQuestions = await repository.loadAll()

// Write — updates cache and persists to file atomically
try await repository.save(newQuestion)
try await repository.delete("q-001")
```

```swift
let repository = LocalRepository<Question>()

// 读取 — 从内存缓存快速 O(1) 查找
let question = await repository.find(by: "q-001")
let allQuestions = await repository.loadAll()

// 写入 — 原子更新缓存并持久化到文件
try await repository.save(newQuestion)
try await repository.delete("q-001")
```

### Combining with @Observable ViewModel
### 与 @Observable ViewModel 结合

```swift
@Observable
final class QuestionListViewModel {
    private(set) var questions: [Question] = []
    private let repository: LocalRepository<Question>

    init(repository: LocalRepository<Question> = LocalRepository()) {
        self.repository = repository
    }

    func load() async {
        questions = await repository.loadAll()
    }

    func add(_ question: Question) async throws {
        try await repository.save(question)
        questions = await repository.loadAll()
    }
}
```

## Key Design Decisions
## 关键设计决策

| Decision | Rationale |
|----------|-----------|
| Actor (not class + lock) | Compiler-enforced thread safety, no manual synchronization |
| In-memory cache + file persistence | Fast reads from cache, durable writes to disk |
| Synchronous init loading | Avoids async initialization complexity |
| Dictionary keyed by ID | O(1) lookups by identifier |
| Generic over `Codable & Identifiable` | Reusable across any model type |
| Atomic file writes (`.atomic`) | Prevents partial writes on crash |

| 决策 | 理由 |
|----------|-----------|
| Actor（而非 class + lock） | 编译器强制线程安全，无需手动同步 |
| 内存缓存 + 文件持久化 | 从缓存快速读取，向磁盘持久写入 |
| 同步 init 加载 | 避免 async 初始化复杂性 |
| 按 ID 键控的字典 | 按标识符 O(1) 查找 |
| 泛型 `Codable & Identifiable` | 可跨任何模型类型复用 |
| 原子文件写入（`.atomic`） | 防止崩溃时部分写入 |

## Best Practices
## 最佳实践

- **Use `Sendable` types** for all data crossing actor boundaries
- **对所有跨 actor 边界的数据使用 `Sendable` 类型**
- **Keep the actor's public API minimal** — only expose domain operations, not persistence details
- **保持 actor 的公共 API 最简** — 仅暴露领域操作，不暴露持久化细节
- **Use `.atomic` writes** to prevent data corruption if the app crashes mid-write
- **使用 `.atomic` 写入** 以防止应用在写入中途崩溃时数据损坏
- **Load synchronously in `init`** — async initializers add complexity with minimal benefit for local files
- **在 `init` 中同步加载** — async 初始化器会增加复杂性，而对本地文件收益甚微
- **Combine with `@Observable`** ViewModels for reactive UI updates
- **与 `@Observable` ViewModels 结合** 以实现响应式 UI 更新

## Anti-Patterns to Avoid
## 应避免的反模式

- Using `DispatchQueue` or `NSLock` instead of actors for new Swift concurrency code
- 对新的 Swift 并发代码使用 `DispatchQueue` 或 `NSLock` 而不是 actors
- Exposing the internal cache dictionary to external callers
- 向外部调用者暴露内部缓存字典
- Making the file URL configurable without validation
- 在没有验证的情况下使文件 URL 可配置
- Forgetting that all actor method calls are `await` — callers must handle async context
- 忘记所有 actor 方法调用都是 `await` — 调用者必须处理 async 上下文
- Using `nonisolated` to bypass actor isolation (defeats the purpose)
- 使用 `nonisolated` 绕过 actor 隔离（违背目的）

## When to Use
## 何时使用

- Local data storage in iOS/macOS apps (user data, settings, cached content)
- iOS/macOS 应用中的本地数据存储（用户数据、设置、缓存内容）
- Offline-first architectures that sync to a server later
- 稍后同步到服务器的离线优先架构
- Any shared mutable state that multiple parts of the app access concurrently
- 应用多个部分并发访问的任何共享可变状态
- Replacing legacy `DispatchQueue`-based thread safety with modern Swift concurrency
- 用现代 Swift 并发替换传统的基于 `DispatchQueue` 的线程安全
