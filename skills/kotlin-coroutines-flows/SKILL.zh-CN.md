---
name: kotlin-coroutines-flows
description: Kotlin Coroutines and Flow patterns for Android and KMP — structured concurrency, Flow operators, StateFlow, error handling, and testing.
description zh-CN: Android 和 KMP 的 Kotlin 协程和 Flow 模式——结构化并发、Flow 操作符、StateFlow、错误处理和测试。
origin: ECC
---

# Kotlin Coroutines & Flows
# Kotlin 协程和 Flow

Patterns for structured concurrency, Flow-based reactive streams, and coroutine testing in Android and Kotlin Multiplatform projects.
Android 和 Kotlin 多平台项目中结构化并发、基于 Flow 的响应式流和协程测试的模式。

## When to Activate
## 何时激活

- Writing async code with Kotlin coroutines
  - 使用 Kotlin 协程编写异步代码
- Using Flow, StateFlow, or SharedFlow for reactive data
  - 使用 Flow、StateFlow 或 SharedFlow 处理响应式数据
- Handling concurrent operations (parallel loading, debounce, retry)
  - 处理并发操作（并行加载、防抖、重试）
- Testing coroutines and Flows
  - 测试协程和 Flow
- Managing coroutine scopes and cancellation
  - 管理协程作用域和取消

## Structured Concurrency
## 结构化并发

### Scope Hierarchy
### 作用域层级

```
Application
  └── viewModelScope (ViewModel)
        └── coroutineScope { } (structured child)
              ├── async { } (concurrent task)
              └── async { } (concurrent task)
```

Always use structured concurrency — never `GlobalScope`:
始终使用结构化并发——绝不使用 `GlobalScope`：

```kotlin
// BAD
GlobalScope.launch { fetchData() }

// GOOD — scoped to ViewModel lifecycle
viewModelScope.launch { fetchData() }

// GOOD — scoped to composable lifecycle
LaunchedEffect(key) { fetchData() }
```

// 错误
GlobalScope.launch { fetchData() }

// 正确——绑定到 ViewModel 生命周期
viewModelScope.launch { fetchData() }

// 正确——绑定到 Compose 生命周期
LaunchedEffect(key) { fetchData() }

### Parallel Decomposition
### 并行分解

Use `coroutineScope` + `async` for parallel work:
使用 `coroutineScope` + `async` 进行并行工作：

```kotlin
suspend fun loadDashboard(): Dashboard = coroutineScope {
    val items = async { itemRepository.getRecent() }
    val stats = async { statsRepository.getToday() }
    val profile = async { userRepository.getCurrent() }
    Dashboard(
        items = items.await(),
        stats = stats.await(),
        profile = profile.await()
    )
}
```

### SupervisorScope
### 监督作用域

Use `supervisorScope` when child failures should not cancel siblings:
当子失败不应取消兄弟任务时使用 `supervisorScope`：

```kotlin
suspend fun syncAll() = supervisorScope {
    launch { syncItems() }       // failure here won't cancel syncStats
    launch { syncStats() }
    launch { syncSettings() }
}
```

suspend fun syncAll() = supervisorScope {
    launch { syncItems() }       // 这里的失败不会取消 syncStats
    launch { syncStats() }
    launch { syncSettings() }
}

## Flow Patterns
## Flow 模式

### Cold Flow — One-Shot to Stream Conversion
### 冷 Flow——一次性到流的转换

```kotlin
fun observeItems(): Flow<List<Item>> = flow {
    // Re-emits whenever the database changes
    itemDao.observeAll()
        .map { entities -> entities.map { it.toDomain() } }
        .collect { emit(it) }
}
```

fun observeItems(): Flow<List<Item>> = flow {
    // 每当数据库变化时重新发射
    itemDao.observeAll()
        .map { entities -> entities.map { it.toDomain() } }
        .collect { emit(it) }
}

### StateFlow for UI State
### 用于 UI 状态的 StateFlow

```kotlin
class DashboardViewModel(
    observeProgress: ObserveUserProgressUseCase
) : ViewModel() {
    val progress: StateFlow<UserProgress> = observeProgress()
        .stateIn(
            scope = viewModelScope,
            started = SharingStarted.WhileSubscribed(5_000),
            initialValue = UserProgress.EMPTY
        )
}
```

`WhileSubscribed(5_000)` keeps the upstream active for 5 seconds after the last subscriber leaves — survives configuration changes without restarting.
`WhileSubscribed(5_000)` 在最后一个订阅者离开后将上游保持活跃 5 秒——在配置变化时存活而不重启。

### Combining Multiple Flows
### 组合多个 Flow

```kotlin
val uiState: StateFlow<HomeState> = combine(
    itemRepository.observeItems(),
    settingsRepository.observeTheme(),
    userRepository.observeProfile()
) { items, theme, profile ->
    HomeState(items = items, theme = theme, profile = profile)
}.stateIn(viewModelScope, SharingStarted.WhileSubscribed(5_000), HomeState())
```

### Flow Operators
### Flow 操作符

```kotlin
// Debounce search input
searchQuery
    .debounce(300)
    .distinctUntilChanged()
    .flatMapLatest { query -> repository.search(query) }
    .catch { emit(emptyList()) }
    .collect { results -> _state.update { it.copy(results = results) } }

// Retry with exponential backoff
fun fetchWithRetry(): Flow<Data> = flow { emit(api.fetch()) }
    .retryWhen { cause, attempt ->
        if (cause is IOException && attempt < 3) {
            delay(1000L * (1 shl attempt.toInt()))
            true
        } else {
            false
        }
    }
```

// 防抖搜索输入
searchQuery
    .debounce(300)
    .distinctUntilChanged()
    .flatMapLatest { query -> repository.search(query) }
    .catch { emit(emptyList()) }
    .collect { results -> _state.update { it.copy(results = results) } }

// 指数退避重试
fun fetchWithRetry(): Flow<Data> = flow { emit(api.fetch()) }
    .retryWhen { cause, attempt ->
        if (cause is IOException && attempt < 3) {
            delay(1000L * (1 shl attempt.toInt()))
            true
        } else {
            false
        }
    }

### SharedFlow for One-Time Events
### 用于一次性事件的 SharedFlow

```kotlin
class ItemListViewModel : ViewModel() {
    private val _effects = MutableSharedFlow<Effect>()
    val effects: SharedFlow<Effect> = _effects.asSharedFlow()

    sealed interface Effect {
        data class ShowSnackbar(val message: String) : Effect
        data class NavigateTo(val route: String) : Effect
    }

    private fun deleteItem(id: String) {
        viewModelScope.launch {
            repository.delete(id)
            _effects.emit(Effect.ShowSnackbar("Item deleted"))
        }
    }
}

// Collect in Composable
LaunchedEffect(Unit) {
    viewModel.effects.collect { effect ->
        when (effect) {
            is Effect.ShowSnackbar -> snackbarHostState.showSnackbar(effect.message)
            is Effect.NavigateTo -> navController.navigate(effect.route)
        }
    }
}
```

## Dispatchers
## 调度器

```kotlin
// CPU-intensive work
withContext(Dispatchers.Default) { parseJson(largePayload) }

// IO-bound work
withContext(Dispatchers.IO) { database.query() }

// Main thread (UI) — default in viewModelScope
withContext(Dispatchers.Main) { updateUi() }
```

// CPU 密集型工作
withContext(Dispatchers.Default) { parseJson(largePayload) }

// IO 密集型工作
withContext(Dispatchers.IO) { database.query() }

// 主线程（UI）——viewModelScope 中的默认值
withContext(Dispatchers.Main) { updateUi() }

In KMP, use `Dispatchers.Default` and `Dispatchers.Main` (available on all platforms). `Dispatchers.IO` is JVM/Android only — use `Dispatchers.Default` on other platforms or provide via DI.
在 KMP 中，使用 `Dispatchers.Default` 和 `Dispatchers.Main`（在所有平台上可用）。`Dispatchers.IO` 仅适用于 JVM/Android——在其他平台上使用 `Dispatchers.Default` 或通过 DI 提供。

## Cancellation
## 取消

### Cooperative Cancellation
### 合作取消

Long-running loops must check for cancellation:
长时间运行的循环必须检查取消：

```kotlin
suspend fun processItems(items: List<Item>) = coroutineScope {
    for (item in items) {
        ensureActive()  // throws CancellationException if cancelled
        process(item)
    }
}
```

suspend fun processItems(items: List<Item>) = coroutineScope {
    for (item in items) {
        ensureActive()  // 如果被取消则抛出 CancellationException
        process(item)
    }
}

### Cleanup with try/finally
### 使用 try/finally 清理

```kotlin
viewModelScope.launch {
    try {
        _state.update { it.copy(isLoading = true) }
        val data = repository.fetch()
        _state.update { it.copy(data = data) }
    } finally {
        _state.update { it.copy(isLoading = false) }  // always runs, even on cancellation
    }
}
```

viewModelScope.launch {
    try {
        _state.update { it.copy(isLoading = true) }
        val data = repository.fetch()
        _state.update { it.copy(data = data) }
    } finally {
        _state.update { it.copy(isLoading = false) }  // 始终运行，即使在取消时
    }
}

## Testing
## 测试

### Testing StateFlow with Turbine
### 使用 Turbine 测试 StateFlow

```kotlin
@Test
fun `search updates item list`() = runTest {
    val fakeRepository = FakeItemRepository().apply { emit(testItems) }
    val viewModel = ItemListViewModel(GetItemsUseCase(fakeRepository))

    viewModel.state.test {
        assertEquals(ItemListState(), awaitItem())  // initial

        viewModel.onSearch("query")
        val loading = awaitItem()
        assertTrue(loading.isLoading)

        val loaded = awaitItem()
        assertFalse(loaded.isLoading)
        assertEquals(1, loaded.items.size)
    }
}
```

### Testing with TestDispatcher
### 使用 TestDispatcher 测试

```kotlin
@Test
fun `parallel load completes correctly`() = runTest {
    val viewModel = DashboardViewModel(
        itemRepo = FakeItemRepo(),
        statsRepo = FakeStatsRepo()
    )

    viewModel.load()
    advanceUntilIdle()

    val state = viewModel.state.value
    assertNotNull(state.items)
    assertNotNull(state.stats)
}
```

### Faking Flows
### 伪造 Flow

```kotlin
class FakeItemRepository : ItemRepository {
    private val _items = MutableStateFlow<List<Item>>(emptyList())

    override fun observeItems(): Flow<List<Item>> = _items

    fun emit(items: List<Item>) { _items.value = items }

    override suspend fun getItemsByCategory(category: String): Result<List<Item>> {
        return Result.success(_items.value.filter { it.category == category })
    }
}
```

## Anti-Patterns to Avoid
## 应避免的反模式

- Using `GlobalScope` — leaks coroutines, no structured cancellation
  - 使用 `GlobalScope`——泄漏协程，没有结构化取消
- Collecting Flows in `init {}` without a scope — use `viewModelScope.launch`
  - 在 `init {}` 中收集 Flow 但没有作用域——使用 `viewModelScope.launch`
- Using `MutableStateFlow` with mutable collections — always use immutable copies: `_state.update { it.copy(list = it.list + newItem) }`
  - 使用可变集合的 `MutableStateFlow`——始终使用不可变副本：`_state.update { it.copy(list = it.list + newItem) }`
- Catching `CancellationException` — let it propagate for proper cancellation
  - 捕获 `CancellationException`——让它传播以实现正确的取消
- Using `flowOn(Dispatchers.Main)` to collect — collection dispatcher is the caller's dispatcher
  - 使用 `flowOn(Dispatchers.Main)` 收集——收集调度器是调用者的调度器
- Creating `Flow` in `@Composable` without `remember` — recreates the flow every recomposition
  - 在 `@Composable` 中创建 `Flow` 但没有 `remember`——每次重组时重新创建 flow

## References
## 参考

See skill: `compose-multiplatform-patterns` for UI consumption of Flows.
参见技能：`compose-multiplatform-patterns` 了解 Flow 的 UI 消费。
See skill: `android-clean-architecture` for where coroutines fit in layers.
参见技能：`android-clean-architecture` 了解协程在各层中的位置。
