---
name: swift-hooks
description: Swift tool hooks configuration
description: Swift 工具钩子配置
paths:
  - "**/*.swift"
  - "**/Package.swift"
---

# Swift Hooks
# Swift Hooks

> This file extends [common/hooks.md](../common/hooks.md) with Swift specific content.
> 此文件扩展了 [common/hooks.md](../common/hooks.md)，包含 Swift 特定内容。

## PostToolUse Hooks
## PostToolUse 钩子

Configure in `~/.claude/settings.json`:
在 `~/.claude/settings.json` 中配置：

- **SwiftFormat**：编辑后自动格式化 `.swift` 文件（**SwiftFormat**: Auto-format `.swift` files after edit）
- **SwiftLint**：编辑 `.swift` 文件后运行 lint 检查（**SwiftLint**: Run lint checks after editing `.swift` files）
- **swift build**：编辑后对修改的包进行类型检查（`swift build`: Type-check modified packages after edit）

## Warning
## 警告

标记 `print()` 语句 — 生产代码改为使用 `os.Logger` 或结构化日志（Flag `print()` statements — use `os.Logger` or structured logging instead for production code.）
