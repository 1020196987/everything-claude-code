---
name: kotlin-testing
description: Kotlin testing frameworks and patterns
description: Kotlin 测试框架和模式
paths:
  - "**/*.kt"
  - "**/*.kts"
---

# Kotlin Testing
# Kotlin 测试

> This file extends [common/testing.md](../common/testing.md) with Kotlin and Android/KMP-specific content.
> 此文件扩展了 [common/testing.md](../common/testing.md)，包含 Kotlin 和 Android/KMP 特定内容。

## Test Framework
## 测试框架

- **kotlin.test** 用于多平台（KMP）— `@Test`、`assertEquals`、`assertTrue`（**kotlin.test** for multiplatform (KMP) — `@Test`, `assertEquals`, `assertTrue`）
- **JUnit 4/5** 用于 Android 特定测试（**JUnit 4/5** for Android-specific tests）
- **Turbine** 用于测试 Flows 和 StateFlow（**Turbine** for testing Flows and StateFlow）
- **kotlinx-coroutines-test** 用于协程测试（`runTest`、`TestDispatcher`）（**kotlinx-coroutines-test** for coroutine testing (`runTest`, `TestDispatcher`)）

## ViewModel Testing with Turbine
## 使用 Turbine 测试 ViewModel

```kotlin
@Test
fun `loading state emitted then data`() = runTest {
    val repo = FakeItemRepository()
    repo.addItem(testItem)
    val viewModel = ItemListViewModel(GetItemsUseCase(repo))

    viewModel.state.test {
        assertEquals(ItemListState(), awaitItem())     // initial state
        viewModel.onEvent(ItemListEvent.Load)
        assertTrue(awaitItem().isLoading)               // loading
        assertEquals(listOf(testItem), awaitItem().items) // loaded
    }
}
```

## Fakes Over Mocks
## 优先使用 Fake 而非 Mock

Prefer hand-written fakes over mocking frameworks:
优先使用手写的 fake 而非 mock 框架：

```kotlin
class FakeItemRepository : ItemRepository {
    private val items = mutableListOf<Item>()
    var fetchError: Throwable? = null

    override suspend fun getAll(): Result<List<Item>> {
        fetchError?.let { return Result.failure(it) }
        return Result.success(items.toList())
    }

    override fun observeAll(): Flow<List<Item>> = flowOf(items.toList())

    fun addItem(item: Item) { items.add(item) }
}
```

## Coroutine Testing
## 协程测试

```kotlin
@Test
fun `parallel operations complete`() = runTest {
    val repo = FakeRepository()
    val result = loadDashboard(repo)
    advanceUntilIdle()
    assertNotNull(result.items)
    assertNotNull(result.stats)
}
```

Use `runTest` — it auto-advances virtual time and provides `TestScope`.
使用 `runTest` — 它自动推进虚拟时间并提供 `TestScope`。

## Ktor MockEngine

```kotlin
val mockEngine = MockEngine { request ->
    when (request.url.encodedPath) {
        "/api/items" -> respond(
            content = Json.encodeToString(testItems),
            headers = headersOf(HttpHeaders.ContentType, ContentType.Application.Json.toString())
        )
        else -> respondError(HttpStatusCode.NotFound)
    }
}

val client = HttpClient(mockEngine) {
    install(ContentNegotiation) { json() }
}
```

## Room/SQLDelight Testing
## Room/SQLDelight 测试

- Room: Use `Room.inMemoryDatabaseBuilder()` for in-memory testing
- Room：使用 `Room.inMemoryDatabaseBuilder()` 进行内存测试
- SQLDelight: Use `JdbcSqliteDriver(JdbcSqlileDriver.IN_MEMORY)` for JVM tests
- SQLDelight：使用 `JdbcSqliteDriver(JdbcSqliteDriver.IN_MEMORY)` 进行 JVM 测试

```kotlin
@Test
fun `insert and query items`() = runTest {
    val driver = JdbcSqliteDriver(JdbcSqliteDriver.IN_MEMORY)
    Database.Schema.create(driver)
    val db = Database(driver)

    db.itemQueries.insert("1", "Sample Item", "description")
    val items = db.itemQueries.getAll().executeAsList()
    assertEquals(1, items.size)
}
```

## Test Naming
## 测试命名

Use backtick-quoted descriptive names:
使用反引号引起来的描述性名称：

```kotlin
@Test
fun `search with empty query returns all items`() = runTest { }

@Test
fun `delete item emits updated list without deleted item`() = runTest { }
```

## Test Organization
## 测试组织

```
src/
├── commonTest/kotlin/     # Shared tests (ViewModel, UseCase, Repository)
├── commonTest/kotlin/     # 共享测试（ViewModel、UseCase、Repository）
├── androidUnitTest/kotlin/ # Android unit tests (JUnit)
├── androidUnitTest/kotlin/ # Android 单元测试（JUnit）
├── androidInstrumentedTest/kotlin/  # Instrumented tests (Room, UI)
├── androidInstrumentedTest/kotlin/  # 仪器化测试（Room、UI）
└── iosTest/kotlin/        # iOS-specific tests
└── iosTest/kotlin/        # iOS 特定测试
```

Minimum test coverage: ViewModel + UseCase for every feature.
最低测试覆盖率：每个功能的 ViewModel + UseCase。
