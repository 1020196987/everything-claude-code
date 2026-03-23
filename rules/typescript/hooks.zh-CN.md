---
name: TypeScript Hooks
name: TypeScript 钩子
description: TypeScript/JavaScript tool hook configuration
description: TypeScript/JavaScript 工具钩子配置
paths:
  - "**/*.ts"
  - "**/*.tsx"
  - "**/*.js"
  - "**/*.jsx"
---
# TypeScript/JavaScript Hooks
# TypeScript/JavaScript 钩子

> This file extends [common/hooks.md](../common/hooks.md) with TypeScript/JavaScript specific content.
> 此文件扩展了 [common/hooks.md](../common/hooks.md)，包含 TypeScript/JavaScript 特定内容。

## PostToolUse Hooks
## PostToolUse 钩子

Configure in `~/.claude/settings.json`:
在 `~/.claude/settings.json` 中配置：

- **Prettier**: Auto-format JS/TS files after edit
  **Prettier**：编辑后自动格式化 JS/TS 文件
- **TypeScript check**: Run `tsc` after editing `.ts`/`.tsx` files
  **TypeScript check**：编辑 `.ts`/`.tsx` 文件后运行 `tsc`
- **console.log warning**: Warn about `console.log` in edited files
  **console.log warning**：警告编辑文件中的 `console.log`

## Stop Hooks
## Stop 钩子

- **console.log audit**: Check all modified files for `console.log` before session ends
  **console.log audit**：在会话结束前检查所有修改文件中的 `console.log`
