---
description: Comprehensive Kotlin code review for idiomatic patterns, null safety, coroutine safety, and security. Invokes the kotlin-reviewer agent.
description-zh: 对 Kotlin 代码进行惯用模式、空安全、协程安全和安全的综合代码审查。调用 kotlin-reviewer agent。
---

# Kotlin Code Review
# Kotlin 代码审查

This command invokes the **kotlin-reviewer** agent for comprehensive Kotlin-specific code review.
此命令调用 **kotlin-reviewer** agent 进行全面的 Kotlin 特定代码审查。

## What This Command Does
## 此命令做什么

1. **Identify Kotlin Changes**: Find modified `.kt` and `.kts` files via `git diff`
1. **识别 Kotlin 更改**：通过 `git diff` 查找修改的 `.kt` 和 `.kts` 文件

2. **Run Build & Static Analysis**: Execute `./gradlew build`, `detekt`, `ktlintCheck`
2. **运行构建和静态分析**：执行 `./gradlew build`、`detekt`、`ktlintCheck`

3. **Security Scan**: Check for SQL injection, command injection, hardcoded secrets
3. **安全扫描**：检查 SQL 注入、命令注入、硬编码秘密

4. **Null Safety Review**: Analyze `!!` usage, platform type handling, unsafe casts
4. **空安全审查**：分析 `!!` 使用、平台类型处理、不安全转换

5. **Coroutine Review**: Check structured concurrency, dispatcher usage, cancellation
5. **协程审查**：检查结构化并发、调度器使用、取消

6. **Generate Report**: Categorize issues by severity
6. **生成报告**：按严重性分类问题

## When to Use
## 使用场景

Use `/kotlin-review` when:
使用 `/kotlin-review` 当：

- After writing or modifying Kotlin code
- 编写或修改 Kotlin 代码后

- Before committing Kotlin changes
- 提交 Kotlin 更改前

- Reviewing pull requests with Kotlin code
- 审查带有 Kotlin 代码的 PR

- Onboarding to a new Kotlin codebase
- 加入新的 Kotlin 代码库

- Learning idiomatic Kotlin patterns
- 学习惯用 Kotlin 模式

## Review Categories
## 审查类别

### CRITICAL (Must Fix)
### 严重（必须修复）

- SQL/Command injection vulnerabilities
- SQL/命令注入漏洞

- Force-unwrap `!!` without justification
- 无理由的强制解包 `!!`

- Platform type null safety violations
- 平台类型空安全违规

- GlobalScope usage (structured concurrency violation)
- GlobalScope 使用（结构化并发违规）

- Hardcoded credentials
- 硬编码凭证

- Unsafe deserialization
- 不安全反序列化

### HIGH (Should Fix)
### 高（应该修复）

- Mutable state where immutable suffices
- 在不可变足够时使用可变状态

- Blocking calls inside coroutine context
- 在协程上下文中使用阻塞调用

- Missing cancellation checks in long loops
- 长循环中缺少取消检查

- Non-exhaustive `when` on sealed types
- sealed 类型上非穷尽的 `when`

- Large functions (>50 lines)
- 大函数（>50 行）

- Deep nesting (>4 levels)
- 深层嵌套（>4 层）

### MEDIUM (Consider)
### 中（考虑修复）

- Non-idiomatic Kotlin (Java-style patterns)
- 非惯用 Kotlin（Java 风格模式）

- Missing trailing commas
- 缺少尾随逗号

- Scope function misuse or nesting
- 作用域函数误用或嵌套

- Missing sequence for large collection chains
- 大集合链缺少 sequence

- Redundant explicit types
- 冗余的显式类型

## Automated Checks Run
## 自动运行检查

```bash
# Build check
# 构建检查
./gradlew build

# Static analysis
# 静态分析
./gradlew detekt

# Formatting check
# 格式检查
./gradlew ktlintCheck

# Tests
# 测试
./gradlew test
```

## Example Usage
## 示例用法

````text
User: /kotlin-review

Agent:
# Kotlin Code Review Report

## Files Reviewed
- src/main/kotlin/com/example/service/UserService.kt (modified)
- src/main/kotlin/com/example/routes/UserRoutes.kt (modified)

## Static Analysis Results
✓ Build: Successful
✓ detekt: No issues
⚠ ktlint: 2 formatting warnings

## Issues Found

[CRITICAL] Force-Unwrap Null Safety
File: src/main/kotlin/com/example/service/UserService.kt:28
Issue: Using !! on nullable repository result
```kotlin
val user = repository.findById(id)!!  // NPE risk
```
Fix: Use safe call with error handling
```kotlin
val user = repository.findById(id)
    ?: throw UserNotFoundException("User $id not found")
```

[HIGH] GlobalScope Usage
File: src/main/kotlin/com/example/routes/UserRoutes.kt:45
Issue: Using GlobalScope breaks structured concurrency
```kotlin
GlobalScope.launch {
    notificationService.sendWelcome(user)
}
```
Fix: Use the call's coroutine scope
```kotlin
launch {
    notificationService.sendWelcome(user)
}
```

## Summary
- CRITICAL: 1
- HIGH: 1
- MEDIUM: 0

Recommendation: ❌ Block merge until CRITICAL issue is fixed
````

## Approval Criteria
## 批准标准

| Status | Condition |
|--------|-----------|
| ✅ Approve | No CRITICAL or HIGH issues |
| ✅ 通过 | 无 CRITICAL 或 HIGH 问题 |
| ⚠️ Warning | Only MEDIUM issues (merge with caution) |
| ⚠️ 警告 | 仅 MEDIUM 问题（谨慎合并） |
| ❌ Block | CRITICAL or HIGH issues found |
| ❌ 阻止 | 发现 CRITICAL 或 HIGH 问题 |

## Integration with Other Commands
## 与其他命令集成

- Use `/kotlin-test` first to ensure tests pass
- 先使用 `/kotlin-test` 确保测试通过

- Use `/kotlin-build` if build errors occur
- 如果发生构建错误，使用 `/kotlin-build`

- Use `/kotlin-review` before committing
- 提交前使用 `/kotlin-review`

- Use `/code-review` for non-Kotlin-specific concerns
- 使用 `/code-review` 处理非 Kotlin 特定问题

## Related
## 相关

- Agent: `agents/kotlin-reviewer.md`
- Skills: `skills/kotlin-patterns/`, `skills/kotlin-testing/`
