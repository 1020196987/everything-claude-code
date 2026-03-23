---
name: kotlin-hooks
description: Kotlin tool hooks configuration
description: Kotlin 工具钩子配置
paths:
  - "**/*.kt"
  - "**/*.kts"
  - "**/build.gradle.kts"
---

# Kotlin Hooks
# Kotlin Hooks

> This file extends [common/hooks.md](../common/hooks.md) with Kotlin-specific content.
> 此文件扩展了 [common/hooks.md](../common/hooks.md)，包含 Kotlin 特定内容。

## PostToolUse Hooks
## PostToolUse 钩子

Configure in `~/.claude/settings.json`:
在 `~/.claude/settings.json` 中配置：

- **ktfmt/ktlint**：编辑后自动格式化 `.kt` 和 `.kts` 文件（**ktfmt/ktlint**: Auto-format `.kt` and `.kts` files after edit）
- **detekt**：编辑 Kotlin 文件后运行静态分析（**detekt**: Run static analysis after editing Kotlin files）
- **./gradlew build**：更改后验证编译（`./gradlew build`: Verify compilation after changes）
