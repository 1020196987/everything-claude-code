---
name: kotlin-coding-style
description: Kotlin coding style guidelines
description: Kotlin 编码风格规范
paths:
  - "**/*.kt"
  - "**/*.kts"
---

# Kotlin Coding Style
# Kotlin 编码风格

> This file extends [common/coding-style.md](../common/coding-style.md) with Kotlin-specific content.
> 此文件扩展了 [common/coding-style.md](../common/coding-style.md)，包含 Kotlin 特定内容。

## Formatting
## 格式化

- **ktlint** or **Detekt** for style enforcement
  **ktlint** 或 **Detekt** 用于风格强制
- Official Kotlin code style (`kotlin.code.style=official` in `gradle.properties`)
  官方 Kotlin 代码风格（`gradle.properties` 中的 `kotlin.code.style=official`）

## Immutability
## 不可变性

- Prefer `val` over `var` — default to `val` and only use `var` when mutation is required
  优先使用 `val` 而非 `var` — 默认使用 `val`，仅在需要变更时使用 `var`
- Use `data class` for value types; use immutable collections (`List`, `Map`, `Set`) in public APIs
  对值类型使用 `data class`；在公共 API 中使用不可变集合（`List`、`Map`、`Set`）
- Copy-on-write for state updates: `state.copy(field = newValue)`
  状态更新使用写时复制：`state.copy(field = newValue)`

## Naming
## 命名

Follow Kotlin conventions:
遵循 Kotlin 约定：
- `camelCase` for functions and properties
  函数和属性使用 `camelCase`
- `PascalCase` for classes, interfaces, objects, and type aliases
  类、接口、对象和类型别名使用 `PascalCase`
- `SCREAMING_SNAKE_CASE` for constants (`const val` or `@JvmStatic`)
  常量使用 `SCREAMING_SNAKE_CASE`（`const val` 或 `@JvmStatic`）
- Prefix interfaces with behavior, not `I`: `Clickable` not `IClickable`
  接口以行为前缀，而非 `I`：`Clickable` 而非 `IClickable`

## Null Safety
## 空安全

- Never use `!!` — prefer `?.`, `?:`, `requireNotNull()`, or `checkNotNull()`
  永不使用 `!!` — 优先使用 `?.`、`?:`、`requireNotNull()` 或 `checkNotNull()`
- Use `?.let {}` for scoped null-safe operations
  使用 `?.let {}` 进行作用域内的空安全操作
- Return nullable types from functions that can legitimately have no result
  对可以合法没有结果的函数返回可空类型

```kotlin
// BAD
// 不好
val name = user!!.name

// GOOD
// 好
val name = user?.name ?: "Unknown"
val name = requireNotNull(user) { "User must be set before accessing name" }.name
```

## Sealed Types
## 密封类型

Use sealed classes/interfaces to model closed state hierarchies:
使用密封类/接口建模封闭状态层次结构：

```kotlin
sealed interface UiState<out T> {
    data object Loading : UiState<Nothing>
    data class Success<T>(val data: T) : UiState<T>
    data class Error(val message: String) : UiState<Nothing>
}
```

Always use exhaustive `when` with sealed types — no `else` branch.
始终对密封类型使用穷尽 `when` — 无 `else` 分支。

## Extension Functions
## 扩展函数

Use extension functions for utility operations, but keep them discoverable:
将扩展函数用于工具操作，但保持其可发现性：
- Place in a file named after the receiver type (`StringExt.kt`, `FlowExt.kt`)
  放在以接收者类型命名的文件中（`StringExt.kt`、`FlowExt.kt`）
- Keep scope limited — don't add extensions to `Any` or overly generic types
  限制作用域 — 不要向 `Any` 或过度通用类型添加扩展

## Scope Functions
## 作用域函数

Use the right scope function:
使用正确的作用域函数：
- `let` — null check + transform: `user?.let { greet(it) }`
  `let` — 空检查 + 转换：`user?.let { greet(it) }`
- `run` — compute a result using receiver: `service.run { fetch(config) }`
  `run` — 使用接收者计算结果：`service.run { fetch(config) }`
- `apply` — configure an object: `builder.apply { timeout = 30 }`
  `apply` — 配置对象：`builder.apply { timeout = 30 }`
- `also` — side effects: `result.also { log(it) }`
  `also` — 副作用：`result.also { log(it) }`
- Avoid deep nesting of scope functions (max 2 levels)
  避免深层嵌套作用域函数（最多 2 层）

## Error Handling
## 错误处理

- Use `Result<T>` or custom sealed types
  使用 `Result<T>` 或自定义密封类型
- Use `runCatching {}` for wrapping throwable code
  使用 `runCatching {}` 包装可抛出代码
- Never catch `CancellationException` — always rethrow it
  永捕获 `CancellationException` — 始终重新抛出它
- Avoid `try-catch` for control flow
  避免使用 `try-catch` 进行控制流

```kotlin
// BAD — using exceptions for control flow
// 不好 — 使用异常进行控制流
val user = try { repository.getUser(id) } catch (e: NotFoundException) { null }

// GOOD — nullable return
// 好 — 可空返回
val user: User? = repository.findUser(id)
```
