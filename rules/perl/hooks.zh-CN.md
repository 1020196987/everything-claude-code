---
name: Perl Hooks
description: Perl Hooks
description: Perl 工具钩子配置
paths:
  - "**/*.pl"
  - "**/*.pm"
  - "**/*.t"
  - "**/*.psgi"
  - "**/*.cgi"
---
# Perl Hooks
# Perl Hooks

> This file extends [common/hooks.md](../common/hooks.md) with Perl-specific content.
> 此文件扩展了 [common/hooks.md](../common/hooks.md)，包含 Perl 特定内容。

## PostToolUse Hooks
## PostToolUse 钩子

Configure in `~/.claude/settings.json`:
在 `~/.claude/settings.json` 中配置：

- **perltidy**: Auto-format `.pl` and `.pm` files after edit
  **perltidy**：编辑后自动格式化 `.pl` 和 `.pm` 文件
- **perlcritic**: Run lint check after editing `.pm` files
  **perlcritic**：编辑 `.pm` 文件后运行 lint 检查

## Warnings
## 警告

- Warn about `print` in non-script `.pm` files — use `say` or a logging module (e.g., `Log::Any`)
  警告在非脚本 `.pm` 文件中的 `print` — 使用 `say` 或日志模块（例如 `Log::Any`）
