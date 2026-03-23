---
description: Fix Gradle build errors for Android and KMP projects
description-zh: 修复 Android 和 KMP 项目的 Gradle 构建错误
---

# Gradle Build Fix
# Gradle 构建修复

Incrementally fix Gradle build and compilation errors for Android and Kotlin Multiplatform projects.
为 Android 和 Kotlin 多平台项目增量修复 Gradle 构建和编译错误。

## Step 1: Detect Build Configuration
## 步骤 1：检测构建配置

Identify the project type and run the appropriate build:
识别项目类型并运行适当的构建：

| Indicator | Build Command |
|-----------|---------------|
| `build.gradle.kts` + `composeApp/` (KMP) | `./gradlew composeApp:compileKotlinMetadata 2>&1` |
| `build.gradle.kts` + `app/` (Android) | `./gradlew app:compileDebugKotlin 2>&1` |
| `settings.gradle.kts` with modules | `./gradlew assemble 2>&1` |
| Detekt configured | `./gradlew detekt 2>&1` |

Also check `gradle.properties` and `local.properties` for configuration.
也检查 `gradle.properties` 和 `local.properties` 的配置。

## Step 2: Parse and Group Errors
## 步骤 2：解析并分组错误

1. Run the build command and capture output
1. 运行构建命令并捕获输出

2. Separate Kotlin compilation errors from Gradle configuration errors
2. 将 Kotlin 编译错误与 Gradle 配置错误分开

3. Group by module and file path
3. 按模块和文件路径分组

4. Sort: configuration errors first, then compilation errors by dependency order
4. 排序：先配置错误，然后按依赖顺序的编译错误

## Step 3: Fix Loop
## 步骤 3：修复循环

For each error:
对于每个错误：

1. **Read the file** — Full context around the error line
1. **读取文件** — 错误行的完整上下文

2. **Diagnose** — Common categories:
2. **诊断** — 常见类别：

   - Missing import or unresolved reference
   - 缺少 import 或无法解析的引用

   - Type mismatch or incompatible types
   - 类型不匹配或不兼容类型

   - Missing dependency in `build.gradle.kts`
   - `build.gradle.kts` 中缺少依赖

   - Expect/actual mismatch (KMP)
   - Expect/actual 不匹配（KMP）

   - Compose compiler error
   - Compose 编译器错误

3. **Fix minimally** — Smallest change that resolves the error
3. **最小修复** — 解决错误的最小更改

4. **Re-run build** — Verify fix and check for new errors
4. **重新运行构建** — 验证修复并检查新错误

5. **Continue** — Move to next error
5. **继续** — 进入下一个错误

## Step 4: Guardrails
## 步骤 4：护栏

Stop and ask the user if:
如果以下情况，停止并询问用户：

- Fix introduces more errors than it resolves
- 修复引入的错误比解决的更多

- Same error persists after 3 attempts
- 同一错误在 3 次尝试后仍然存在

- Error requires adding new dependencies or changing module structure
- 错误需要添加新依赖或更改模块结构

- Gradle sync itself fails (configuration-phase error)
- Gradle sync 本身失败（配置阶段错误）

- Error is in generated code (Room, SQLDelight, KSP)
- 错误在生成的代码中（Room、SQLDelight、KSP）

## Step 5: Summary
## 步骤 5：总结

Report:
报告：

- Errors fixed (module, file, description)
- 已修复的错误（模块、文件、描述）

- Errors remaining
- 剩余错误

- New errors introduced (should be zero)
- 引入的新错误（应该为零）

- Suggested next steps
- 建议的后续步骤

## Common Gradle/KMP Fixes
## 常见 Gradle/KMP 修复

| Error | Fix |
|-------|-----|
| Unresolved reference in `commonMain` | Check if the dependency is in `commonMain.dependencies {}` |
| `commonMain` 中无法解析的引用 | 检查依赖是否在 `commonMain.dependencies {}` 中 |
| Expect declaration without actual | Add `actual` implementation in each platform source set |
| Expect 声明没有 actual | 在每个平台源集中添加 `actual` 实现 |
| Compose compiler version mismatch | Align Kotlin and Compose compiler versions in `libs.versions.toml` |
| Compose 编译器版本不匹配 | 在 `libs.versions.toml` 中对齐 Kotlin 和 Compose 编译器版本 |
| Duplicate class | Check for conflicting dependencies with `./gradlew dependencies` |
| 重复类 | 用 `./gradlew dependencies` 检查冲突的依赖 |
| KSP error | Run `./gradlew kspCommonMainKotlinMetadata` to regenerate |
| KSP 错误 | 运行 `./gradlew kspCommonMainKotlinMetadata` 重新生成 |
| Configuration cache issue | Check for non-serializable task inputs |
| 配置缓存问题 | 检查不可序列化的任务输入 |
