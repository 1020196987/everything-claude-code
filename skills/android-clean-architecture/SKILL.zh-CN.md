---
name: android-clean-architecture
description: Clean Architecture patterns for Android and Kotlin Multiplatform projects — module structure, dependency rules, UseCases, Repositories, and data layer patterns.
description zh-CN: Android 和 Kotlin Multiplatform 项目的整洁架构模式——模块结构、依赖规则、UseCase、Repository 和数据层模式。
origin: ECC
---

# Android Clean Architecture

# Android Clean Architecture
# Android 整洁架构

Clean Architecture patterns for Android and KMP projects. Covers module boundaries, dependency inversion, UseCase/Repository patterns, and data layer design with Room, SQLDelight, and Ktor.
Android 和 KMP 项目的整洁架构模式。涵盖模块边界、依赖反转、UseCase/Repository 模式，以及使用 Room、SQLDelight 和 Ktor 的数据层设计。

## When to Activate

## When to Activate
## 何时激活

- Structuring Android or KMP project modules
  - 构建 Android 或 KMP 项目模块
- Implementing UseCases, Repositories, or DataSources
  - 实现 UseCase、Repository 或 DataSource
- Designing data flow between layers (domain, data, presentation)
  - 设计层间数据流（domain、data、presentation）
- Setting up dependency injection with Koin or Hilt
  - 使用 Koin 或 Hilt 设置依赖注入
- Working with Room, SQLDelight, or Ktor in a layered architecture
  - 在分层架构中使用 Room、SQLDelight 或 Ktor

## Module Structure

## Module Structure
## 模块结构

### Recommended Layout

### Recommended Layout
### 推荐的目录布局

```
project/
├── app/                  # Android entry point, DI wiring, Application class
├── app/                  # Android 入口点，DI 接线，Application 类
├── core/                 # Shared utilities, base classes, error types
├── core/                 # 共享工具类、基类、错误类型
├── domain/               # UseCases, domain models, repository interfaces (pure Kotlin)
├── domain/               # UseCase、领域模型、repository 接口（纯 Kotlin）
├── data/                 # Repository implementations, DataSources, DB, network
├── data/                 # Repository 实现、DataSource、数据库、网络
├── presentation/         # Screens, ViewModels, UI models, navigation
├── presentation/         # 屏幕、ViewModel、UI 模型、导航
├── design-system/        # Reusable Compose components, theme, typography
├── design-system/        # 可复用的 Compose 组件、主题、字体
└── feature/              # Feature modules (optional, for larger projects)
└── feature/              # 功能模块（可选，用于大型项目）
    ├── auth/
    ├── auth/
    ├── settings/
    ├── settings/
    └── profile/
    └── profile/
```

### Dependency Rules

### Dependency Rules
### 依赖规则

```
app → presentation, domain, data, core
presentation → domain, design-system, core
data → domain, core
domain → core (or no dependencies)
core → (nothing)
app → presentation, domain, data, core
presentation → domain, design-system, core
data → domain, core
domain → core（或无依赖）
core → （无）
```

**Critical**: `domain` must NEVER depend on `data`, `presentation`, or any framework. It contains pure Kotlin only.
**关键**：`domain` 绝对不能依赖 `data`、`presentation` 或任何框架。它只包含纯 Kotlin。

## Domain Layer

## Domain Layer
## Domain 层

### UseCase Pattern

### UseCase Pattern
### UseCase 模式

Each UseCase represents one business operation. Use `operator fun invoke` for clean call sites:
每个 UseCase 代表一个业务操作。使用 `operator fun invoke` 以获得简洁的调用点：

```kotlin
class GetItemsByCategoryUseCase(
    private val repository: ItemRepository
) {
    suspend operator fun invoke(category: String): Result<List<Item>> {
        return repository.getItemsByCategory(category)
    }
}

// Flow-based UseCase for reactive streams
// 用于响应式流的基于 Flow 的 UseCase
class ObserveUserProgressUseCase(
    private val repository: UserRepository
) {
    operator fun invoke(userId: String): Flow<UserProgress> {
        return repository.observeProgress(userId)
    }
}
```

### Domain Models

### Domain Models
### 领域模型

Domain models are plain Kotlin data classes — no framework annotations:
领域模型是纯 Kotlin 数据类——没有框架注解：

```kotlin
data class Item(
    val id: String,
    val title: String,
    val description: String,
    val tags: List<String>,
    val status: Status,
    val category: String
)

enum class Status { DRAFT, ACTIVE, ARCHIVED }
```

### Repository Interfaces

### Repository Interfaces
### Repository 接口

Defined in domain, implemented in data:
在 domain 中定义，在 data 中实现：

```kotlin
interface ItemRepository {
    suspend fun getItemsByCategory(category: String): Result<List<Item>>
    suspend fun saveItem(item: Item): Result<Unit>
    fun observeItems(): Flow<List<Item>>
}
```

## Data Layer

## Data Layer
## Data 层

### Repository Implementation

### Repository Implementation
### Repository 实现

Coordinates between local and remote data sources:
协调本地和远程数据源：

```kotlin
class ItemRepositoryImpl(
    private val localDataSource: ItemLocalDataSource,
    private val remoteDataSource: ItemRemoteDataSource
) : ItemRepository {

    override suspend fun getItemsByCategory(category: String): Result<List<Item>> {
        return runCatching {
            val remote = remoteDataSource.fetchItems(category)
            localDataSource.insertItems(remote.map { it.toEntity() })
            localDataSource.getItemsByCategory(category).map { it.toDomain() }
        }
    }

    override suspend fun saveItem(item: Item): Result<Unit> {
        return runCatching {
            localDataSource.insertItems(listOf(item.toEntity()))
        }
    }

    override fun observeItems(): Flow<List<Item>> {
        return localDataSource.observeAll().map { entities ->
            entities.map { it.toDomain() }
        }
    }
}
```

### Mapper Pattern

### Mapper Pattern
### 映射器模式

Keep mappers as extension functions near the data models:
将映射器作为扩展函数放在数据模型附近：

```kotlin
// In data layer
// 在 data 层
fun ItemEntity.toDomain() = Item(
    id = id,
    title = title,
    description = description,
    tags = tags.split("|"),
    status = Status.valueOf(status),
    category = category
)

fun ItemDto.toEntity() = ItemEntity(
    id = id,
    title = title,
    description = description,
    tags = tags.joinToString("|"),
    status = status,
    category = category
)
```

### Room Database (Android)

### Room Database (Android)
### Room 数据库（Android）

```kotlin
@Entity(tableName = "items")
data class ItemEntity(
    @PrimaryKey val id: String,
    val title: String,
    val description: String,
    val tags: String,
    val status: String,
    val category: String
)

@Dao
interface ItemDao {
    @Query("SELECT * FROM items WHERE category = :category")
    suspend fun getByCategory(category: String): List<ItemEntity>

    @Upsert
    suspend fun upsert(items: List<ItemEntity>)

    @Query("SELECT * FROM items")
    fun observeAll(): Flow<List<ItemEntity>>
}
```

### SQLDelight (KMP)

### SQLDelight (KMP)
### SQLDelight（KMP）

```sql
-- Item.sq
CREATE TABLE ItemEntity (
    id TEXT NOT NULL PRIMARY KEY,
    title TEXT NOT NULL,
    description TEXT NOT NULL,
    tags TEXT NOT NULL,
    status TEXT NOT NULL,
    category TEXT NOT NULL
);

getByCategory:
SELECT * FROM ItemEntity WHERE category = ?;

upsert:
INSERT OR REPLACE INTO ItemEntity (id, title, description, tags, status, category)
VALUES (?, ?, ?, ?, ?, ?);

observeAll:
SELECT * FROM ItemEntity;
```

### Ktor Network Client (KMP)

### Ktor Network Client (KMP)
### Ktor 网络客户端（KMP）

```kotlin
class ItemRemoteDataSource(private val client: HttpClient) {

    suspend fun fetchItems(category: String): List<ItemDto> {
        return client.get("api/items") {
            parameter("category", category)
        }.body()
    }
}

// HttpClient setup with content negotiation
// 带内容协商的 HttpClient 设置
val httpClient = HttpClient {
    install(ContentNegotiation) { json(Json { ignoreUnknownKeys = true }) }
    install(Logging) { level = LogLevel.HEADERS }
    defaultRequest { url("https://api.example.com/") }
}
```

## Dependency Injection

## Dependency Injection
## 依赖注入

### Koin (KMP-friendly)

### Koin (KMP-friendly)
### Koin（KMP 友好）

```kotlin
// Domain module
// Domain 模块
val domainModule = module {
    factory { GetItemsByCategoryUseCase(get()) }
    factory { ObserveUserProgressUseCase(get()) }
}

// Data module
// Data 模块
val dataModule = module {
    single<ItemRepository> { ItemRepositoryImpl(get(), get()) }
    single { ItemLocalDataSource(get()) }
    single { ItemRemoteDataSource(get()) }
}

// Presentation module
// Presentation 模块
val presentationModule = module {
    viewModelOf(::ItemListViewModel)
    viewModelOf(::DashboardViewModel)
}
```

### Hilt (Android-only)

### Hilt (Android-only)
### Hilt（仅 Android）

```kotlin
@Module
@InstallIn(SingletonComponent::class)
abstract class RepositoryModule {
    @Binds
    abstract fun bindItemRepository(impl: ItemRepositoryImpl): ItemRepository
}

@HiltViewModel
class ItemListViewModel @Inject constructor(
    private val getItems: GetItemsByCategoryUseCase
) : ViewModel()
```

## Error Handling

## Error Handling
## 错误处理

### Result/Try Pattern

### Result/Try Pattern
### Result/Try 模式

Use `Result<T>` or a custom sealed type for error propagation:
使用 `Result<T>` 或自定义 sealed 类型进行错误传播：

```kotlin
sealed interface Try<out T> {
    data class Success<T>(val value: T) : Try<T>
    data class Failure(val error: AppError) : Try<Nothing>
}

sealed interface AppError {
    data class Network(val message: String) : AppError
    data class Database(val message: String) : AppError
    data object Unauthorized : AppError
}

// In ViewModel — map to UI state
// 在 ViewModel 中——映射到 UI 状态
viewModelScope.launch {
    when (val result = getItems(category)) {
        is Try.Success -> _state.update { it.copy(items = result.value, isLoading = false) }
        is Try.Failure -> _state.update { it.copy(error = result.error.toMessage(), isLoading = false) }
    }
}
```

## Convention Plugins (Gradle)

## Convention Plugins (Gradle)
## 约定插件（Gradle）

For KMP projects, use convention plugins to reduce build file duplication:
对于 KMP 项目，使用约定插件以减少构建文件重复：

```kotlin
// build-logic/src/main/kotlin/kmp-library.gradle.kts
plugins {
    id("org.jetbrains.kotlin.multiplatform")
}

kotlin {
    androidTarget()
    iosX64(); iosArm64(); iosSimulatorArm64()
    sourceSets {
        commonMain.dependencies { /* shared deps */ }
        commonTest.dependencies { implementation(kotlin("test")) }
    }
}
```

Apply in modules:
在模块中应用：

```kotlin
// domain/build.gradle.kts
plugins { id("kmp-library") }
```

## Anti-Patterns to Avoid

## Anti-Patterns to Avoid
## 应避免的反模式

- Importing Android framework classes in `domain` — keep it pure Kotlin
  - 在 `domain` 中导入 Android 框架类——保持纯 Kotlin
- Exposing database entities or DTOs to the UI layer — always map to domain models
  - 向 UI 层暴露数据库实体或 DTO——始终映射到领域模型
- Putting business logic in ViewModels — extract to UseCases
  - 将业务逻辑放在 ViewModel 中——提取到 UseCase
- Using `GlobalScope` or unstructured coroutines — use `viewModelScope` or structured concurrency
  - 使用 `GlobalScope` 或非结构化协程——使用 `viewModelScope` 或结构化并发
- Fat repository implementations — split into focused DataSources
  - 臃肿的 repository 实现——拆分为专注的 DataSource
- Circular module dependencies — if A depends on B, B must not depend on A
  - 循环模块依赖——如果 A 依赖 B，B 不能依赖 A

## References

## References
## 参考资料

See skill: `compose-multiplatform-patterns` for UI patterns.
参见技能：`compose-multiplatform-patterns`（UI 模式）。
See skill: `kotlin-coroutines-flows` for async patterns.
参见技能：`kotlin-coroutines-flows`（异步模式）。
