---
name: swift-concurrency-6-2
description: Swift 6.2 Approachable Concurrency — single-threaded by default, @concurrent for explicit background offloading, isolated conformances for main actor types.
description zh-CN: Swift 6.2 平易并发 — 默认单线程，@concurrent 用于显式后台卸载，主 actor 类型的隔离遵循。
---

# Swift 6.2 Approachable Concurrency
# Swift 6.2 平易并发

Patterns for adopting Swift 6.2's concurrency model where code runs single-threaded by default and concurrency is introduced explicitly. Eliminates common data-race errors without sacrificing performance.
采用 Swift 6.2 并发模型的模式，其中代码默认单线程运行，并显式引入并发。在不牺牲性能的情况下消除常见的数据竞争错误。

## When to Activate
## 何时激活

- Migrating Swift 5.x or 6.0/6.1 projects to Swift 6.2
- 将 Swift 5.x 或 6.0/6.1 项目迁移到 Swift 6.2
- Resolving data-race safety compiler errors
- 解决数据竞争安全编译器错误
- Designing MainActor-based app architecture
- 设计基于 MainActor 的应用架构
- Offloading CPU-intensive work to background threads
- 将 CPU 密集型工作卸载到后台线程
- Implementing protocol conformances on MainActor-isolated types
- 在 MainActor 隔离的类型上实现协议遵循
- Enabling Approachable Concurrency build settings in Xcode 26
- 在 Xcode 26 中启用平易并发构建设置

## Core Problem: Implicit Background Offloading
## 核心问题：隐式后台卸载

In Swift 6.1 and earlier, async functions could be implicitly offloaded to background threads, causing data-race errors even in seemingly safe code:
在 Swift 6.1 及更早版本中，async 函数可能隐式卸载到后台线程，即使在看似安全的代码中也会导致数据竞争错误：

```swift
// Swift 6.1: ERROR
@MainActor
final class StickerModel {
    let photoProcessor = PhotoProcessor()

    func extractSticker(_ item: PhotosPickerItem) async throws -> Sticker? {
        guard let data = try await item.loadTransferable(type: Data.self) else { return nil }

        // Error: Sending 'self.photoProcessor' risks causing data races
        return await photoProcessor.extractSticker(data: data, with: item.itemIdentifier)
    }
}
```

Swift 6.2 fixes this: async functions stay on the calling actor by default.
Swift 6.2 修复了这个问题：async 函数默认保留在调用 actor 上。

```swift
// Swift 6.2: OK — async stays on MainActor, no data race
@MainActor
final class StickerModel {
    let photoProcessor = PhotoProcessor()

    func extractSticker(_ item: PhotosPickerItem) async throws -> Sticker? {
        guard let data = try await item.loadTransferable(type: Data.self) else { return nil }
        return await photoProcessor.extractSticker(data: data, with: item.itemIdentifier)
    }
}
```

## Core Pattern — Isolated Conformances
## 核心模式 — 隔离遵循

MainActor types can now conform to non-isolated protocols safely:
MainActor 类型现在可以安全地遵循非隔离协议：

```swift
protocol Exportable {
    func export()
}

// Swift 6.1: ERROR — crosses into main actor-isolated code
// Swift 6.2: OK with isolated conformance
extension StickerModel: @MainActor Exportable {
    func export() {
        photoProcessor.exportAsPNG()
    }
}
```

The compiler ensures the conformance is only used on the main actor:
编译器确保该遵循仅在 main actor 上使用：

```swift
// OK — ImageExporter is also @MainActor
@MainActor
struct ImageExporter {
    var items: [any Exportable]

    mutating func add(_ item: StickerModel) {
        items.append(item)  // Safe: same actor isolation
    }
}

// ERROR — nonisolated context can't use MainActor conformance
nonisolated struct ImageExporter {
    var items: [any Exportable]

    mutating func add(_ item: StickerModel) {
        items.append(item)  // Error: Main actor-isolated conformance cannot be used here
    }
}
```

## Core Pattern — Global and Static Variables
## 核心模式 — 全局和静态变量

Protect global/static state with MainActor:
用 MainActor 保护全局/静态状态：

```swift
// Swift 6.1: ERROR — non-Sendable type may have shared mutable state
final class StickerLibrary {
    static let shared: StickerLibrary = .init()  // Error
}

// Fix: Annotate with @MainActor
@MainActor
final class StickerLibrary {
    static let shared: StickerLibrary = .init()  // OK
}
```

### MainActor Default Inference Mode
### MainActor 默认推断模式

Swift 6.2 introduces a mode where MainActor is inferred by default — no manual annotations needed:
Swift 6.2 引入了一种模式，其中 MainActor 默认推断 — 无需手动注解：

```swift
// With MainActor default inference enabled:
final class StickerLibrary {
    static let shared: StickerLibrary = .init()  // Implicitly @MainActor
}

final class StickerModel {
    let photoProcessor: PhotoProcessor
    var selection: [PhotosPickerItem]  // Implicitly @MainActor
}

extension StickerModel: Exportable {  // Implicitly @MainActor conformance
    func export() {
        photoProcessor.exportAsPNG()
    }
}
```

This mode is opt-in and recommended for apps, scripts, and other executable targets.
此模式是可选的，推荐用于应用、脚本和其他可执行目标。

## Core Pattern — @concurrent for Background Work
## 核心模式 — @concurrent 用于后台工作

When you need actual parallelism, explicitly offload with `@concurrent`:
当需要实际并行时，用 `@concurrent` 显式卸载：

> **Important:** This example requires Approachable Concurrency build settings — SE-0466 (MainActor default isolation) and SE-0461 (NonisolatedNonsendingByDefault). With these enabled, `extractSticker` stays on the caller's actor, making mutable state access safe. **Without these settings, this code has a data race** — the compiler will flag it.
> **重要：** 此示例需要平易并发构建设置 — SE-0466（MainActor 默认隔离）和 SE-0461（NonisolatedNonsendingByDefault）。启用这些设置后，`extractSticker` 保留在调用者 actor 上，使可变状态访问安全。**没有这些设置，此代码存在数据竞争** — 编译器将标记它。

```swift
nonisolated final class PhotoProcessor {
    private var cachedStickers: [String: Sticker] = [:]

    func extractSticker(data: Data, with id: String) async -> Sticker {
        if let sticker = cachedStickers[id] {
            return sticker
        }

        let sticker = await Self.extractSubject(from: data)
        cachedStickers[id] = sticker
        return sticker
    }

    // Offload expensive work to concurrent thread pool
    @concurrent
    static func extractSubject(from data: Data) async -> Sticker { /* ... */ }
}

// Callers must await
let processor = PhotoProcessor()
processedPhotos[item.id] = await processor.extractSticker(data: data, with: item.id)
```

To use `@concurrent`:
要使用 `@concurrent`：

1. Mark the containing type as `nonisolated`
1. 将包含类型标记为 `nonisolated`
2. Add `@concurrent` to the function
2. 在函数上添加 `@concurrent`
3. Add `async` if not already asynchronous
3. 如果尚不是异步则添加 `async`
4. Add `await` at call sites
4. 在调用点添加 `await`

## Key Design Decisions
## 关键设计决策

| Decision | Rationale |
|----------|-----------|
| Single-threaded by default | Most natural code is data-race free; concurrency is opt-in |
| Async stays on calling actor | Eliminates implicit offloading that caused data-race errors |
| Isolated conformances | MainActor types can conform to protocols without unsafe workarounds |
| `@concurrent` explicit opt-in | Background execution is a deliberate performance choice, not accidental |
| MainActor default inference | Reduces boilerplate `@MainActor` annotations for app targets |
| Opt-in adoption | Non-breaking migration path — enable features incrementally |

| 决策 | 理由 |
|----------|-----------|
| 默认单线程 | 最自然的代码无数据竞争；并发是可选的 |
| Async 保留在调用 actor 上 | 消除了导致数据竞争错误的隐式卸载 |
| 隔离遵循 | MainActor 类型可以在没有不安全变通方法的情况下遵循协议 |
| `@concurrent` 显式选择加入 | 后台执行是深思熟虑的性能选择，而非偶然 |
| MainActor 默认推断 | 为应用目标减少样板 `@MainActor` 注解 |
| 可选采用 | 非破坏性迁移路径 — 逐步启用功能 |

## Migration Steps
## 迁移步骤

1. **Enable in Xcode**: Swift Compiler > Concurrency section in Build Settings
1. **在 Xcode 中启用**：构建设置的 Swift Compiler > Concurrency 部分
2. **Enable in SPM**: Use `SwiftSettings` API in package manifest
2. **在 SPM 中启用**：在包清单中使用 `SwiftSettings` API
3. **Use migration tooling**: Automatic code changes via swift.org/migration
3. **使用迁移工具**：通过 swift.org/migration 进行自动代码更改
4. **Start with MainActor defaults**: Enable inference mode for app targets
4. **从 MainActor 默认值开始**：为应用目标启用推断模式
5. **Add `@concurrent` where needed**: Profile first, then offload hot paths
5. **在需要时添加 `@concurrent`**：先进行性能分析，然后卸载热点路径
6. **Test thoroughly**: Data-race issues become compile-time errors
6. **彻底测试**：数据竞争问题变为编译时错误

## Best Practices
## 最佳实践

- **Start on MainActor** — write single-threaded code first, optimize later
- **从 MainActor 开始** — 先编写单线程代码，以后优化
- **Use `@concurrent` only for CPU-intensive work** — image processing, compression, complex computation
- **仅对 CPU 密集型工作使用 `@concurrent`** — 图像处理、压缩、复杂计算
- **Enable MainActor inference mode** for app targets that are mostly single-threaded
- **为大多为单线程的应用目标启用 MainActor 推断模式**
- **Profile before offloading** — use Instruments to find actual bottlenecks
- **卸载前先性能分析** — 使用 Instruments 找到实际的瓶颈
- **Protect globals with MainActor** — global/static mutable state needs actor isolation
- **用 MainActor 保护全局变量** — 全局/静态可变状态需要 actor 隔离
- **Use isolated conformances** instead of `nonisolated` workarounds or `@Sendable` wrappers
- **使用隔离遵循** 而不是 `nonisolated` 变通方法或 `@Sendable` 包装器
- **Migrate incrementally** — enable features one at a time in build settings
- **逐步迁移** — 在构建设置中一次启用一个功能

## Anti-Patterns to Avoid
## 应避免的反模式

- Applying `@concurrent` to every async function (most don't need background execution)
- 对每个 async 函数应用 `@concurrent`（大多数不需要后台执行）
- Using `nonisolated` to suppress compiler errors without understanding isolation
- 使用 `nonisolated` 来抑制编译器错误而不理解隔离
- Keeping legacy `DispatchQueue` patterns when actors provide the same safety
- 当 actors 提供相同安全性时保留传统的 `DispatchQueue` 模式
- Skipping `model.availability` checks in concurrency-related Foundation Models code
- 在与并发相关的 Foundation Models 代码中跳过 `model.availability` 检查
- Fighting the compiler — if it reports a data race, the code has a real concurrency issue
- 与编译器对抗 — 如果它报告数据竞争，代码确实存在并发问题
- Assuming all async code runs in the background (Swift 6.2 default: stays on calling actor)
- 假设所有 async 代码都在后台运行（Swift 6.2 默认：保留在调用 actor 上）

## When to Use
## 何时使用

- All new Swift 6.2+ projects (Approachable Concurrency is the recommended default)
- 所有新的 Swift 6.2+ 项目（平易并发是推荐的默认设置）
- Migrating existing apps from Swift 5.x or 6.0/6.1 concurrency
- 从 Swift 5.x 或 6.0/6.1 并发迁移现有应用
- Resolving data-race safety compiler errors during Xcode 26 adoption
- 在采用 Xcode 26 期间解决数据竞争安全编译器错误
- Building MainActor-centric app architectures (most UI apps)
- 构建以 MainActor 为中心的应用架构（大多数 UI 应用）
- Performance optimization — offloading specific heavy computations to background
- 性能优化 — 将特定的繁重计算卸载到后台
