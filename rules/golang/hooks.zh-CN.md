---
name: hooks
description: Go tool hooks configuration
description: Go 工具钩子配置
---

# Hooks
# Go Hooks

> This file extends [common/hooks.md](../common/hooks.md) with Go specific content.
> 此文件扩展了 [common/hooks.md](../common/hooks.md)，包含 Go 特定内容。

## PostToolUse Hooks
## PostToolUse 钩子

Configure in `~/.claude/settings.json`:
在 `~/.claude/settings.json` 中配置：

- **gofmt/goimports**：编辑后自动格式化 `.go` 文件（`gofmt/goimports`: Auto-format `.go` files after edit）
- **go vet**：编辑 `.go` 文件后运行静态分析（`go vet`: Run static analysis after editing `.go` files）
- **staticcheck**：对修改的包运行扩展静态检查（`staticcheck`: Run extended static checks on modified packages）
