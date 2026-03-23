---
description: Fix Kotlin/Gradle build errors, compiler warnings, and dependency issues incrementally. Invokes the kotlin-build-resolver agent for minimal, surgical fixes.
description-zh: 增量修复 Kotlin/Gradle 构建错误、编译器警告和依赖问题。调用 kotlin-build-resolver agent 进行最小的、精确的修复。
---

# Kotlin Build and Fix
# Kotlin 构建和修复

This command invokes the **kotlin-build-resolver** agent to incrementally fix Kotlin build errors with minimal changes.
此命令调用 **kotlin-build-resolver** agent 以最小的更改增量修复 Kotlin 构建错误。

## What This Command Does
## 此命令做什么

1. **Run Diagnostics**: Execute `./gradlew build`, `detekt`, `ktlintCheck`
1. **运行诊断**：执行 `./gradlew build`、`detekt`、`ktlintCheck`

2. **Parse Errors**: Group by file and sort by severity
2. **解析错误**：按文件分组，按严重性排序

3. **Fix Incrementally**: One error at a time
3. **增量修复**：一次一个错误

4. **Verify Each Fix**: Re-run build after each change
4. **验证每个修复**：每次更改后重新运行构建

5. **Report Summary**: Show what was fixed and what remains
5. **报告总结**：显示已修复和剩余内容

## When to Use
## 使用场景

Use `/kotlin-build` when:
使用 `/kotlin-build` 当：

- `./gradlew build` fails with errors
- `./gradlew build` 失败并报错

- Kotlin compiler reports errors
- Kotlin 编译器报告错误

- `./gradlew detekt` reports violations
- `./gradlew detekt` 报告违规

- Gradle dependency resolution fails
- Gradle 依赖解析失败

- After pulling changes that break the build
- 拉取更改后破坏构建

## Diagnostic Commands Run
## 运行的诊断命令

```bash
# Primary build check
# 主要构建检查
./gradlew build 2>&1

# Static analysis
# 静态分析
./gradlew detekt 2>&1 || echo "detekt not configured"
./gradlew ktlintCheck 2>&1 || echo "ktlint not configured"

# Dependency issues
# 依赖问题
./gradlew dependencies --configuration runtimeClasspath 2>&1 | head -100

# Optional deep refresh when caches or dependency metadata are suspect
# 当缓存或依赖元数据有问题时的可选深度刷新
./gradlew build --refresh-dependencies
```

## Example Session
## 示例会话

````text
User: /kotlin-build

Agent:
# Kotlin Build Resolution

## Initial Diagnostics

```bash
$ ./gradlew build
e: src/main/kotlin/com/example/service/UserService.kt:25:15 Unresolved reference: UserRepository
e: src/main/kotlin/com/example/routes/UserRoutes.kt:42:9 Type mismatch: inferred type is String but Int was expected
e: src/main/kotlin/com/example/routes/UserRoutes.kt:58:5 'when' expression must be exhaustive
```

Errors found: 3

## Fix 1: Unresolved Reference

File: src/main/kotlin/com/example/service/UserService.kt:25
Error: Unresolved reference: UserRepository
Cause: Missing import

```kotlin
// Added import
import com.example.repository.UserRepository
```

```bash
$ ./gradlew build
# 2 errors remaining
```

## Fix 2: Type Mismatch

File: src/main/kotlin/com/example/routes/UserRoutes.kt:42
Error: Type mismatch: inferred type is String but Int was expected

```kotlin
// Changed
val count = call.parameters["count"]
// To
val count = call.parameters["count"]?.toIntOrNull()
    ?: return@get call.respond(HttpStatusCode.BadRequest, "Invalid count")
```

```bash
$ ./gradlew build
# 1 error remaining
```

## Fix 3: Non-Exhaustive When

File: src/main/kotlin/com/example/routes/UserRoutes.kt:58
Error: 'when' expression must be exhaustive

```kotlin
// Added missing branch
when (user.role) {
    Role.ADMIN -> handleAdmin(user)
    Role.USER -> handleUser(user)
    Role.MODERATOR -> handleModerator(user) // Added
}
```

```bash
$ ./gradlew build
# Build successful!
```

## Final Verification

```bash
$ ./gradlew detekt
# No issues

$ ./gradlew test
# All tests passed
```

## Summary

| Metric | Count |
|--------|-------|
| Build errors fixed | 3 |
| Detekt issues fixed | 0 |
| Files modified | 2 |
| Remaining issues | 0 |

Build Status: ✅ SUCCESS
````

## Common Errors Fixed
## 常见修复的错误

| Error | Typical Fix |
|-------|-------------|
| `Unresolved reference: X` | Add import or dependency |
| `未解析的引用：X` | 添加 import 或依赖 |
| `Type mismatch` | Fix type conversion or assignment |
| `类型不匹配` | 修复类型转换或赋值 |
| `'when' must be exhaustive` | Add missing sealed class branches |
| `'when' 必须穷尽` | 添加缺失的 sealed class 分支 |
| `Suspend function can only be called from coroutine` | Add `suspend` modifier |
| `Suspend 函数只能从协程调用` | 添加 `suspend` 修饰符 |
| `Smart cast impossible` | Use local `val` or `let` |
| `智能转换不可能` | 使用局部 `val` 或 `let` |
| `None of the following candidates is applicable` | Fix argument types |
| `以下候选项都不适用` | 修复参数类型 |
| `Could not resolve dependency` | Fix version or add repository |
| `无法解析依赖` | 修复版本或添加仓库 |

## Fix Strategy
## 修复策略

1. **Build errors first** - Code must compile
1. **先构建错误** - 代码必须能编译

2. **Detekt violations second** - Fix code quality issues
2. **其次 Detekt 违规** - 修复代码质量问题

3. **ktlint warnings third** - Fix formatting
3. **然后 ktlint 警告** - 修复格式

4. **One fix at a time** - Verify each change
4. **一次一个修复** - 验证每次更改

5. **Minimal changes** - Don't refactor, just fix
5. **最小更改** - 不重构，只修复

## Stop Conditions
## 停止条件

The agent will stop and report if:
如果以下情况，agent 将停止并报告：

- Same error persists after 3 attempts
- 同一错误在 3 次尝试后仍然存在

- Fix introduces more errors
- 修复引入了更多错误

- Requires architectural changes
- 需要架构更改

- Missing external dependencies
- 缺少外部依赖

## Related Commands
## 相关命令

- `/kotlin-test` - Run tests after build succeeds
- `/kotlin-test` - 构建成功后运行测试

- `/kotlin-review` - Review code quality
- `/kotlin-review` - 审查代码质量

- `/verify` - Full verification loop
- `/verify` - 完整验证循环

## Related
## 相关

- Agent: `agents/kotlin-build-resolver.md`
- Skill: `skills/kotlin-patterns/`
