---
name: swiftui-patterns
description: SwiftUI architecture patterns, state management with @Observable, view composition, navigation, performance optimization, and modern iOS/macOS UI best practices.
description zh-CN: SwiftUI 架构模式、使用 @Observable 的状态管理、视图组合、导航、性能优化以及现代 iOS/macOS UI 最佳实践。
---

# SwiftUI Patterns
# SwiftUI 模式

Modern SwiftUI patterns for building declarative, performant user interfaces on Apple platforms. Covers the Observation framework, view composition, type-safe navigation, and performance optimization.
在 Apple 平台上构建声明式、高性能用户界面的现代 SwiftUI 模式。涵盖 Observation 框架、视图组合、类型安全导航和性能优化。

## When to Activate
## 何时激活

- Building SwiftUI views and managing state (`@State`, `@Observable`, `@Binding`)
- 构建 SwiftUI 视图和管理状态（`@State`、`@Observable`、`@Binding`）
- Designing navigation flows with `NavigationStack`
- 使用 `NavigationStack` 设计导航流程
- Structuring view models and data flow
- 构建视图模型和数据流
- Optimizing rendering performance for lists and complex layouts
- 优化列表和复杂布局的渲染性能
- Working with environment values and dependency injection in SwiftUI
- 在 SwiftUI 中使用环境值和依赖注入

## State Management
## 状态管理

### Property Wrapper Selection
### 属性包装器选择

Choose the simplest wrapper that fits:
选择最简单的适合的包装器：

| Wrapper | Use Case |
|---------|----------|
| `@State` | View-local value types (toggles, form fields, sheet presentation) |
| `@Binding` | Two-way reference to parent's `@State` |
| `@Observable` class + `@State` | Owned model with multiple properties |
| `@Observable` class (no wrapper) | Read-only reference passed from parent |
| `@Bindable` | Two-way binding to an `@Observable` property |
| `@Environment` | Shared dependencies injected via `.environment()` |

| 包装器 | 用例 |
|---------|----------|
| `@State` | 视图本地值类型（开关、表单字段、sheet 呈现） |
| `@Binding` | 到父级 `@State` 的双向引用 |
| `@Observable` class + `@State` | 具有多个属性的自有模型 |
| `@Observable` class（无包装器） | 从父级传递的只读引用 |
| `@Bindable` | 到 `@Observable` 属性的双向绑定 |
| `@Environment` | 通过 `.environment()` 注入的共享依赖 |

### @Observable ViewModel
### @Observable ViewModel

Use `@Observable` (not `ObservableObject`) — it tracks property-level changes so SwiftUI only re-renders views that read the changed property:
使用 `@Observable`（而非 `ObservableObject`）— 它跟踪属性级更改，因此 SwiftUI 仅重新渲染读取已更改属性的视图：

```swift
@Observable
final class ItemListViewModel {
    private(set) var items: [Item] = []
    private(set) var isLoading = false
    var searchText = ""

    private let repository: any ItemRepository

    init(repository: any ItemRepository = DefaultItemRepository()) {
        self.repository = repository
    }

    func load() async {
        isLoading = true
        defer { isLoading = false }
        items = (try? await repository.fetchAll()) ?? []
    }
}
```

### View Consuming the ViewModel
### 使用 ViewModel 的视图

```swift
struct ItemListView: View {
    @State private var viewModel: ItemListViewModel

    init(viewModel: ItemListViewModel = ItemListViewModel()) {
        _viewModel = State(initialValue: viewModel)
    }

    var body: some View {
        List(viewModel.items) { item in
            ItemRow(item: item)
        }
        .searchable(text: $viewModel.searchText)
        .overlay { if viewModel.isLoading { ProgressView() } }
        .task { await viewModel.load() }
    }
}
```

### Environment Injection
### 环境注入

Replace `@EnvironmentObject` with `@Environment`:
用 `@Environment` 替换 `@EnvironmentObject`：

```swift
// Inject
ContentView()
    .environment(authManager)

// Consume
struct ProfileView: View {
    @Environment(AuthManager.self) private var auth

    var body: some View {
        Text(auth.currentUser?.name ?? "Guest")
    }
}
```

## View Composition
## 视图组合

### Extract Subviews to Limit Invalidation
### 提取子视图以限制失效

Break views into small, focused structs. When state changes, only the subview reading that state re-renders:
将视图拆分为小的、专注的 struct。当状态更改时，仅重新渲染读取该状态的子视图：

```swift
struct OrderView: View {
    @State private var viewModel = OrderViewModel()

    var body: some View {
        VStack {
            OrderHeader(title: viewModel.title)
            OrderItemList(items: viewModel.items)
            OrderTotal(total: viewModel.total)
        }
    }
}
```

### ViewModifier for Reusable Styling
### ViewModifier 用于可重用样式

```swift
struct CardModifier: ViewModifier {
    func body(content: Content) -> some View {
        content
            .padding()
            .background(.regularMaterial)
            .clipShape(RoundedRectangle(cornerRadius: 12))
    }
}

extension View {
    func cardStyle() -> some View {
        modifier(CardModifier())
    }
}
```

## Navigation
## 导航

### Type-Safe NavigationStack
### 类型安全 NavigationStack

Use `NavigationStack` with `NavigationPath` for programmatic, type-safe routing:
使用带有 `NavigationPath` 的 `NavigationStack` 进行编程式、类型安全路由：

```swift
@Observable
final class Router {
    var path = NavigationPath()

    func navigate(to destination: Destination) {
        path.append(destination)
    }

    func popToRoot() {
        path = NavigationPath()
    }
}

enum Destination: Hashable {
    case detail(Item.ID)
    case settings
    case profile(User.ID)
}

struct RootView: View {
    @State private var router = Router()

    var body: some View {
        NavigationStack(path: $router.path) {
            HomeView()
                .navigationDestination(for: Destination.self) { dest in
                    switch dest {
                    case .detail(let id): ItemDetailView(itemID: id)
                    case .settings: SettingsView()
                    case .profile(let id): ProfileView(userID: id)
                    }
                }
        }
        .environment(router)
    }
}
```

## Performance
## 性能

### Use Lazy Containers for Large Collections
### 对大集合使用惰性容器

`LazyVStack` and `LazyHStack` create views only when visible:
`LazyVStack` 和 `LazyHStack` 仅在可见时创建视图：

```swift
ScrollView {
    LazyVStack(spacing: 8) {
        ForEach(items) { item in
            ItemRow(item: item)
        }
    }
}
```

### Stable Identifiers
### 稳定标识符

Always use stable, unique IDs in `ForEach` — avoid using array indices:
始终在 `ForEach` 中使用稳定的唯一 ID — 避免使用数组索引：

```swift
// Use Identifiable conformance or explicit id
ForEach(items, id: \.stableID) { item in
    ItemRow(item: item)
}
```

### Avoid Expensive Work in body
### 避免在 body 中执行昂贵工作

- Never perform I/O, network calls, or heavy computation inside `body`
- 永远不要在 `body` 内执行 I/O、网络调用或重计算
- Use `.task {}` for async work — it cancels automatically when the view disappears
- 使用 `.task {}` 进行异步工作 — 它在视图消失时自动取消
- Use `.sensoryFeedback()` and `.geometryGroup()` sparingly in scroll views
- 在滚动视图中谨慎使用 `.sensoryFeedback()` 和 `.geometryGroup()`
- Minimize `.shadow()`, `.blur()`, and `.mask()` in lists — they trigger offscreen rendering
- 在列表中最小化 `.shadow()`、`.blur()` 和 `.mask()` — 它们会触发屏幕外渲染

### Equatable Conformance
### Equatable 遵循

For views with expensive bodies, conform to `Equatable` to skip unnecessary re-renders:
对于有昂贵 body 的视图，遵循 `Equatable` 以跳过不必要的重新渲染：

```swift
struct ExpensiveChartView: View, Equatable {
    let dataPoints: [DataPoint] // DataPoint must conform to Equatable

    static func == (lhs: Self, rhs: Self) -> Bool {
        lhs.dataPoints == rhs.dataPoints
    }

    var body: some View {
        // Complex chart rendering
    }
}
```

## Previews
## 预览

Use `#Preview` macro with inline mock data for fast iteration:
使用 `#Preview` 宏和内联模拟数据进行快速迭代：

```swift
#Preview("Empty state") {
    ItemListView(viewModel: ItemListViewModel(repository: EmptyMockRepository()))
}

#Preview("Loaded") {
    ItemListView(viewModel: ItemListViewModel(repository: PopulatedMockRepository()))
}
```

## Anti-Patterns to Avoid
## 应避免的反模式

- Using `ObservableObject` / `@Published` / `@StateObject` / `@EnvironmentObject` in new code — migrate to `@Observable`
- 在新代码中使用 `ObservableObject`/`@Published`/`@StateObject`/`@EnvironmentObject` — 迁移到 `@Observable`
- Putting async work directly in `body` or `init` — use `.task {}` or explicit load methods
- 将异步工作直接放在 `body` 或 `init` 中 — 使用 `.task {}` 或显式加载方法
- Creating view models as `@State` inside child views that don't own the data — pass from parent instead
- 在不拥有数据的子视图内创建 `@State` 的视图模型 — 而是从父级传递
- Using `AnyView` type erasure — prefer `@ViewBuilder` or `Group` for conditional views
- 使用 `AnyView` 类型擦除 — 对于条件视图，首选 `@ViewBuilder` 或 `Group`
- Ignoring `Sendable` requirements when passing data to/from actors
- 在向/从 actors 传递数据时忽略 `Sendable` 要求

## References
## 参考

See skill: `swift-actor-persistence` for actor-based persistence patterns.
请参阅技能：`swift-actor-persistence` 了解基于 actor 的持久化模式。
See skill: `swift-protocol-di-testing` for protocol-based DI and testing with Swift Testing.
请参阅技能：`swift-protocol-di-testing` 了解基于协议的 DI 和使用 Swift Testing 进行测试。
