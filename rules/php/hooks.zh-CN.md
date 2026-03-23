---
name: PHP Hooks
description: PHP Hooks
description: PHP 工具钩子配置
paths:
  - "**/*.php"
  - "**/composer.json"
  - "**/phpstan.neon"
  - "**/phpstan.neon.dist"
  - "**/psalm.xml"
---
# PHP Hooks
# PHP Hooks

> This file extends [common/hooks.md](../common/hooks.md) with PHP specific content.
> 此文件扩展了 [common/hooks.md](../common/hooks.md)，包含 PHP 特定内容。

## PostToolUse Hooks
## PostToolUse 钩子

Configure in `~/.claude/settings.json`:
在 `~/.claude/settings.json` 中配置：

- **Pint / PHP-CS-Fixer**: Auto-format edited `.php` files.
  **Pint / PHP-CS-Fixer**：自动格式化编辑的 `.php` 文件。
- **PHPStan / Psalm**: Run static analysis after PHP edits in typed codebases.
  **PHPStan / Psalm**：在类型化代码库中编辑 PHP 后运行静态分析。
- **PHPUnit / Pest**: Run targeted tests for touched files or modules when edits affect behavior.
  **PHPUnit / Pest**：当编辑影响行为时，对触发的文件或模块运行针对性测试。

## Warnings
## 警告

- Warn on `var_dump`, `dd`, `dump`, or `die()` left in edited files.
  警告编辑文件中残留的 `var_dump`、`dd`、`dump` 或 `die()`。
- Warn when edited PHP files add raw SQL or disable CSRF/session protections.
  警告编辑的 PHP 文件添加原始 SQL 或禁用 CSRF/session 保护。
