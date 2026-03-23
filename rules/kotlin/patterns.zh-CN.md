---
name: kotlin-patterns
description: Kotlin design patterns and architecture
description: Kotlin 设计模式和架构
paths:
  - "**/*.kt"
  - "**/*.kts"
---

# Kotlin Patterns
# Kotlin 模式

> This file extends [common/patterns.md](../common/patterns.md) with Kotlin and Android/KMP-specific content.
> 此文件扩展了 [common/patterns.md](../common/patterns.md)，包含 Kotlin 和 Android/KMP 特定内容。

## Dependency Injection
## 依赖注入

Prefer constructor injection. Use Koin (KMP) or Hilt (Android-only):
优先使用构造函数注入。使用 Koin（KMP）或 Hilt（仅 Android）：

```kotlin
// Koin — declare modules
// Koin — 声明模块
val dataModule = module {
    single<ItemRepository> { ItemRepositoryImpl(get(), get()) }
    factory { GetItemsUseCase(get()) }
    viewModelOf(::ItemListViewModel)
}

// Hilt — annotations
// Hilt — 注解
@HiltViewModel
class ItemListViewModel @Inject constructor(
    private val getItems: GetItemsUseCase
) : ViewModel()
```

## ViewModel Pattern
## ViewModel 模式

Single state object, event sink, one-way data flow:
单一状态对象，事件接收器，单向数据流：

```kotlin
data class ScreenState(
    val items: List<Item> = emptyList(),
    val isLoading: Boolean = false
)

class ScreenViewModel(private val useCase: GetItemsUseCase) : ViewModel() {
    private val _state = MutableStateFlow(ScreenState())
    val state = _state.asStateFlow()

    fun onEvent(event: ScreenEvent) {
        when (event) {
            is ScreenEvent.Load -> load()
            is ScreenEvent.Delete -> delete(event.id)
        }
    }
}
```

## Repository Pattern
## 仓储模式

- `suspend` functions return `Result<T>` or custom error type
- `suspend` 函数返回 `Result<T>` 或自定义错误类型
- `Flow` for reactive streams
- `Flow` 用于响应式流
- Coordinate local + remote data sources
- 协调本地 + 远程数据源

```kotlin
interface ItemRepository {
    suspend fun getById(id: String): Result<Item>
    suspend fun getAll(): Result<List<Item>>
    fun observeAll(): Flow<List<Item>>
}
```

## UseCase Pattern
## UseCase 模式

Single responsibility, `operator fun invoke`:
单一职责，`operator fun invoke`：

```kotlin
class GetItemUseCase(private val repository: ItemRepository) {
    suspend operator fun invoke(id: String): Result<Item> {
        return repository.getById(id)
    }
}

class GetItemsUseCase(private val repository: ItemRepository) {
    suspend operator fun invoke(): Result<List<Item>> {
        return repository.getAll()
    }
}
```

## expect/actual (KMP)

Use for platform-specific implementations:
用于平台特定实现：

```kotlin
// commonMain
expect fun platformName(): String
expect class SecureStorage {
    fun save(key: String, value: String)
    fun get(key: String): String?
}

// androidMain
actual fun platformName(): String = "Android"
actual class SecureStorage {
    actual fun save(key: String, value: String) { /* EncryptedSharedPreferences */ }
    actual fun get(key: String): String? = null /* ... */
}

// iosMain
actual fun platformName(): String = "iOS"
actual class SecureStorage {
    actual fun save(key: String, value: String) { /* Keychain */ }
    actual fun get(key: String): String? = null /* ... */
}
```

## Coroutine Patterns
## 协程模式

- Use `viewModelScope` in ViewModels, `coroutineScope` for structured child work
- 在 ViewModel 中使用 `viewModelScope`，使用 `coroutineScope` 进行结构化子工作
- Use `stateIn(viewModelScope, SharingStarted.WhileSubscribed(5_000), initialValue)` for StateFlow from cold Flows
- 对来自冷 Flow 的 StateFlow 使用 `stateIn(viewModelScope, SharingStarted.WhileSubscribed(5_000), initialValue)`
- Use `supervisorScope` when child failures should be independent
- 当子失败应该独立时使用 `supervisorScope`

## Builder Pattern with DSL
## 构建器模式和 DSL

```kotlin
class HttpClientConfig {
    var baseUrl: String = ""
    var timeout: Long = 30_000
    private val interceptors = mutableListOf<Interceptor>()

    fun interceptor(block: () -> Interceptor) {
        interceptors.add(block())
    }
}

fun httpClient(block: HttpClientConfig.() -> Unit): HttpClient {
    val config = HttpClientConfig().apply(block)
    return HttpClient(config)
}

// Usage
// 使用
val client = httpClient {
    baseUrl = "https://api.example.com"
    timeout = 15_000
    interceptor { AuthInterceptor(tokenProvider) }
}
```

## References
## 参考

See skill: `kotlin-coroutines-flows` for detailed coroutine patterns.
参见 skill: `kotlin-coroutines-flows` 了解详细的协程模式。
See skill: `android-clean-architecture` for module and layer patterns.
参见 skill: `android-clean-architecture` 了解模块和层模式。
