---
name: hooks
description: Python tool hooks configuration
description: Python 工具钩子配置
---

# Hooks
# Python Hooks

> This file extends [common/hooks.md](../common/hooks.md) with Python specific content.
> 此文件扩展了 [common/hooks.md](../common/hooks.md)，包含 Python 特定内容。

## PostToolUse Hooks
## PostToolUse 钩子

Configure in `~/.claude/settings.json`:
在 `~/.claude/settings.json` 中配置：

- **black/ruff**: Auto-format `.py` files after edit
  **black/ruff**：编辑后自动格式化 `.py` 文件
- **mypy/pyright**: Run type checking after editing `.py` files
  **mypy/pyright**：编辑 `.py` 文件后运行类型检查

## Warnings
## 警告

- Warn about `print()` statements in edited files (use `logging` module instead)
  警告编辑文件中的 `print()` 语句（改为使用 `logging` 模块）
