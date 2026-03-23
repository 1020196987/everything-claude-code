---
name: Perl Coding Style
description: Perl Coding Style
description: Perl 编码风格规范
paths:
  - "**/*.pl"
  - "**/*.pm"
  - "**/*.t"
  - "**/*.psgi"
  - "**/*.cgi"
---
# Perl Coding Style
# Perl 编码风格

> This file extends [common/coding-style.md](../common/coding-style.md) with Perl-specific content.
> 此文件扩展了 [common/coding-style.md](../common/coding-style.md)，包含 Perl 特定内容。

## Standards
## 标准

- Always `use v5.36` (enables `strict`, `warnings`, `say`, subroutine signatures)
- 始终使用 `use v5.36`（启用 `strict`、`warnings`、`say`、子程序签名）
- Use subroutine signatures — never unpack `@_` manually
- 使用子程序签名 — 永不解包 `@_` 手动
- Prefer `say` over `print` with explicit newlines
- 优先使用 `say` 而非带显式换行符的 `print`

## Immutability
## 不可变性

- Use **Moo** with `is => 'ro'` and `Types::Standard` for all attributes
- 对所有属性使用 **Moo** 配合 `is => 'ro'` 和 `Types::Standard`
- Never use blessed hashrefs directly — always use Moo/Moose accessors
- 永不使用受祝福的 hashref — 始终使用 Moo/Moose 访问器
- **OO override note**: Moo `has` attributes with `builder` or `default` are acceptable for computed read-only values
- **OO 覆盖说明**：带有 `builder` 或 `default` 的 Moo `has` 属性可用于计算只读值

## Formatting
## 格式化

Use **perltidy** with these settings:
使用 **perltidy** 和以下设置：

```
-i=4    # 4-space indent
-l=100  # 100 char line length
-ce     # cuddled else
-bar    # opening brace always right
-i=4    # 4 空格缩进
-l=100  # 100 字符行长度
-ce     # 紧凑 else
-bar    # 开括号总是在右边
```

## Linting
## Linting

Use **perlcritic** at severity 3 with themes: `core`, `pbp`, `security`.
使用严重级别 3 的 **perlcritic**，主题：`core`、`pbp`、`security`。

```bash
perlcritic --severity 3 --theme 'core || pbp || security' lib/
```

## Reference
## 参考

See skill: `perl-patterns` for comprehensive modern Perl idioms and best practices.
参见 skill: `perl-patterns` 了解全面的现代 Perl 惯用法和最佳实践。
