---
name: compose-multiplatform-patterns
description: Compose Multiplatform and Jetpack Compose patterns for KMP projects — state management, navigation, theming, performance, and platform-specific UI.
description zh-CN: Compose Multiplatform 和 Jetpack Compose 的 KMP 项目模式——状态管理、导航、主题、性能和平台特定 UI。
origin: ECC
---

# Compose Multiplatform Patterns
# Compose Multiplatform 模式

Patterns for building shared UI across Android, iOS, Desktop, and Web using Compose Multiplatform and Jetpack Compose. Covers state management, navigation, theming, and performance.
使用 Compose Multiplatform 和 Jetpack Compose 构建跨 Android、iOS、桌面和 Web 共享 UI 的模式。涵盖状态管理、导航、主题和性能。

## When to Activate
## 何时激活

- Building Compose UI (Jetpack Compose or Compose Multiplatform)
  - 构建 Compose UI（Jetpack Compose 或 Compose Multiplatform）
- Managing UI state with ViewModels and Compose state
  - 使用 ViewModels 和 Compose 状态管理 UI 状态
- Implementing navigation in KMP or Android projects
  - 在 KMP 或 Android 项目中实现导航
- Designing reusable composables and design systems
  - 设计可复用组合组件和设计系统
- Optimizing recomposition and rendering performance
  - 优化重组和渲染性能

## State Management
## 状态管理

### ViewModel + Single State Object
### ViewModel + 单一状态对象

Use a single data class for screen state. Expose it as `StateFlow` and collect in Compose:
为屏幕状态使用单一数据类。将其暴露为 `StateFlow` 并在 Compose 中收集：

```kotlin
data class ItemListState(
    val items: List<Item> = emptyList(),
    val isLoading: Boolean = false,
    val error: String? = null,
    val searchQuery: String = ""
)

class ItemListViewModel(
    private val getItems: GetItemsUseCase
) : ViewModel() {
    private val _state = MutableStateFlow(ItemListState())
    val state: StateFlow<ItemListState> = _state.asStateFlow()

    fun onSearch(query: String) {
        _state.update { it.copy(searchQuery = query) }
        loadItems(query)
    }

    private fun loadItems(query: String) {
        viewModelScope.launch {
            _state.update { it.copy(isLoading = true) }
            getItems(query).fold(
                onSuccess = { items -> _state.update { it.copy(items = items, isLoading = false) } },
                onFailure = { e -> _state.update { it.copy(error = e.message, isLoading = false) } }
            )
        }
    }
}
```

### Collecting State in Compose
### 在 Compose 中收集状态

```kotlin
@Composable
fun ItemListScreen(viewModel: ItemListViewModel = koinViewModel()) {
    val state by viewModel.state.collectAsStateWithLifecycle()

    ItemListContent(
        state = state,
        onSearch = viewModel::onSearch
    )
}

@Composable
private fun ItemListContent(
    state: ItemListState,
    onSearch: (String) -> Unit
) {
    // Stateless composable — easy to preview and test
    // 无状态组合组件——易于预览和测试
}
```

### Event Sink Pattern
### 事件接收器模式

For complex screens, use a sealed interface for events instead of multiple callback lambdas:
对于复杂屏幕，使用密封接口处理事件而非多个回调 lambda：

```kotlin
sealed interface ItemListEvent {
    data class Search(val query: String) : ItemListEvent
    data class Delete(val itemId: String) : ItemListEvent
    data object Refresh : ItemListEvent
}

// In ViewModel
// 在 ViewModel 中
fun onEvent(event: ItemListEvent) {
    when (event) {
        is ItemListEvent.Search -> onSearch(event.query)
        is ItemListEvent.Delete -> deleteItem(event.itemId)
        is ItemListEvent.Refresh -> loadItems(_state.value.searchQuery)
    }
}

// In Composable — single lambda instead of many
// 在组合组件中——单个 lambda 而非多个
ItemListContent(
    state = state,
    onEvent = viewModel::onEvent
)
```

## Navigation
## 导航

### Type-Safe Navigation (Compose Navigation 2.8+)
### 类型安全导航（Compose Navigation 2.8+）

Define routes as `@Serializable` objects:
将路由定义为 `@Serializable` 对象：

```kotlin
@Serializable data object HomeRoute
@Serializable data class DetailRoute(val id: String)
@Serializable data object SettingsRoute

@Composable
fun AppNavHost(navController: NavHostController = rememberNavController()) {
    NavHost(navController, startDestination = HomeRoute) {
        composable<HomeRoute> {
            HomeScreen(onNavigateToDetail = { id -> navController.navigate(DetailRoute(id)) })
        }
        composable<DetailRoute> { backStackEntry ->
            val route = backStackEntry.toRoute<DetailRoute>()
            DetailScreen(id = route.id)
        }
        composable<SettingsRoute> { SettingsScreen() }
    }
}
```

### Dialog and Bottom Sheet Navigation
### 对话框和底部表单导航

Use `dialog()` and overlay patterns instead of imperative show/hide:
使用 `dialog()` 和叠加模式而非命令式的 show/hide：

```kotlin
NavHost(navController, startDestination = HomeRoute) {
    composable<HomeRoute> { /* ... */ }
    dialog<ConfirmDeleteRoute> { backStackEntry ->
        val route = backStackEntry.toRoute<ConfirmDeleteRoute>()
        ConfirmDeleteDialog(
            itemId = route.itemId,
            onConfirm = { navController.popBackStack() },
            onDismiss = { navController.popBackStack() }
        )
    }
}
```

## Composable Design
## 组合组件设计

### Slot-Based APIs
### 插槽式 API

Design composables with slot parameters for flexibility:
设计带插槽参数的可组合组件以提高灵活性：

```kotlin
@Composable
fun AppCard(
    modifier: Modifier = Modifier,
    header: @Composable () -> Unit = {},
    content: @Composable ColumnScope.() -> Unit,
    actions: @Composable RowScope.() -> Unit = {}
) {
    Card(modifier = modifier) {
        Column {
            header()
            Column(content = content)
            Row(horizontalArrangement = Arrangement.End, content = actions)
        }
    }
}
```

### Modifier Ordering
### 修饰符排序

Modifier order matters — apply in this sequence:
修饰符顺序很重要——按此顺序应用：

```kotlin
Text(
    text = "Hello",
    modifier = Modifier
        .padding(16.dp)          // 1. Layout (padding, size)
        .clip(RoundedCornerShape(8.dp))  // 2. Shape
        .background(Color.White) // 3. Drawing (background, border)
        .clickable { }           // 4. Interaction
)
```

## KMP Platform-Specific UI
## KMP 平台特定 UI

### expect/actual for Platform Composables
### 使用 expect/actual 处理平台组合组件

```kotlin
// commonMain
@Composable
expect fun PlatformStatusBar(darkIcons: Boolean)

// androidMain
@Composable
actual fun PlatformStatusBar(darkIcons: Boolean) {
    val systemUiController = rememberSystemUiController()
    SideEffect { systemUiController.setStatusBarColor(Color.Transparent, darkIcons) }
}

// iosMain
@Composable
actual fun PlatformStatusBar(darkIcons: Boolean) {
    // iOS handles this via UIKit interop or Info.plist
}
```

## Performance
## 性能

### Stable Types for Skippable Recomposition
### 用于可跳过重组的稳定类型

Mark classes as `@Stable` or `@Immutable` when all properties are stable:
当所有属性都稳定时，将类标记为 `@Stable` 或 `@Immutable`：

```kotlin
@Immutable
data class ItemUiModel(
    val id: String,
    val title: String,
    val description: String,
    val progress: Float
)
```

### Use `key()` and Lazy Lists Correctly
### 正确使用 `key()` 和懒列表

```kotlin
LazyColumn {
    items(
        items = items,
        key = { it.id }  // Stable keys enable item reuse and animations
    ) { item ->
        ItemRow(item = item)
    }
}
```

### Defer Reads with `derivedStateOf`
### 使用 `derivedStateOf` 延迟读取

```kotlin
val listState = rememberLazyListState()
val showScrollToTop by remember {
    derivedStateOf { listState.firstVisibleItemIndex > 5 }
}
```

### Avoid Allocations in Recomposition
### 避免重组中的分配

```kotlin
// BAD — new lambda and list every recomposition
// 差——每次重组新 lambda 和列表
items.filter { it.isActive }.forEach { ActiveItem(it, onClick = { handle(it) }) }

// GOOD — key each item so callbacks stay attached to the right row
// 好——为每个 item 设置 key 以使回调保持在正确的行上
val activeItems = remember(items) { items.filter { it.isActive } }
activeItems.forEach { item ->
    key(item.id) {
        ActiveItem(item, onClick = { handle(item) })
    }
}
```

## Theming
## 主题

### Material 3 Dynamic Theming
### Material 3 动态主题

```kotlin
@Composable
fun AppTheme(
    darkTheme: Boolean = isSystemInDarkTheme(),
    dynamicColor: Boolean = true,
    content: @Composable () -> Unit
) {
    val colorScheme = when {
        dynamicColor && Build.VERSION.SDK_INT >= Build.VERSION_CODES.S -> {
            if (darkTheme) dynamicDarkColorScheme(LocalContext.current)
            else dynamicLightColorScheme(LocalContext.current)
        }
        darkTheme -> darkColorScheme()
        else -> lightColorScheme()
    }

    MaterialTheme(colorScheme = colorScheme, content = content)
}
```

## Anti-Patterns to Avoid
## 应避免的反模式

- Using `mutableStateOf` in ViewModels when `MutableStateFlow` with `collectAsStateWithLifecycle` is safer for lifecycle
  - 在 ViewModels 中使用 `mutableStateOf`，而 `MutableStateFlow` 配合 `collectAsStateWithLifecycle` 对生命周期更安全
- Passing `NavController` deep into composables — pass lambda callbacks instead
  - 将 `NavController` 深层传递到组合组件——改为传递 lambda 回调
- Heavy computation inside `@Composable` functions — move to ViewModel or `remember {}`
  - 在 `@Composable` 函数内进行重计算——移至 ViewModel 或 `remember {}`
- Using `LaunchedEffect(Unit)` as a substitute for ViewModel init — it re-runs on configuration change in some setups
  - 使用 `LaunchedEffect(Unit)` 替代 ViewModel 初始化——在某些设置中会在配置更改时重新运行
- Creating new object instances in composable parameters — causes unnecessary recomposition
  - 在组合组件参数中创建新对象实例——导致不必要的重组

## References
## 参考

See skill: `android-clean-architecture` for module structure and layering.
请参阅技能：`android-clean-architecture` 了解模块结构和分层。
See skill: `kotlin-coroutines-flows` for coroutine and Flow patterns.
请参阅技能：`kotlin-coroutines-flows` 了解协程和 Flow 模式。
