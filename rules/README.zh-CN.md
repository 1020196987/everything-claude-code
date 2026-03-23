---
name: Rules
description: Claude Code development rules and best practices
description: Claude Code 开发规则和最佳实践
---

# Rules
# 规则

## Structure
## 结构

Rules are organized into a **common** layer plus **language-specific** directories:
规则分为**通用**层和**语言特定**目录：

```
rules/
├── common/          # Language-agnostic principles (always install)
├── typescript/      # TypeScript/JavaScript specific
├── python/          # Python specific
├── golang/          # Go specific
├── swift/           # Swift specific
└── php/             # PHP specific
```

```
rules/
├── common/          # 语言无关的原则（始终安装）
├── typescript/      # TypeScript/JavaScript 特定
├── python/          # Python 特定
├── golang/          # Go 特定
├── swift/           # Swift 特定
└── php/             # PHP 特定
```

- **common/** contains universal principles — no language-specific code examples.
  **common/** 包含通用原则 — 不含语言特定的代码示例。

- **Language directories** extend the common rules with framework-specific patterns, tools, and code examples. Each file references its common counterpart.
  **语言目录** 通过框架特定的模式、工具和代码示例扩展通用规则。每个文件都引用其对应的通用文件。

## Installation
## 安装

### Option 1: Install Script (Recommended)
### 选项 1：安装脚本（推荐）

```bash
# Install common + one or more language-specific rule sets
./install.sh typescript
./install.sh python
./install.sh golang
./install.sh swift
./install.sh php

# Install multiple languages at once
./install.sh typescript python
```

```bash
# 安装通用规则 + 一个或多个语言特定的规则集
./install.sh typescript
./install.sh python
./install.sh golang
./install.sh swift
./install.sh php

# 一次性安装多个语言
./install.sh typescript python
```

### Option 2: Manual Installation
### 选项 2：手动安装

> **Important:** Copy entire directories — do NOT flatten with `/*`.
> **重要：** 复制整个目录 — 不要使用 `/*` 扁平化。

> Common and language-specific directories contain files with the same names.
> Flattening them into one directory causes language-specific files to overwrite
> common rules, and breaks the relative `../common/` references used by
> language-specific files.
> 通用目录和语言特定目录包含相同名称的文件。
> 将它们扁平化到一个目录会导致语言特定文件覆盖通用规则，
> 并破坏语言特定文件使用的相对 `../common/` 引用。

```bash
# Install common rules (required for all projects)
cp -r rules/common ~/.claude/rules/common

# Install language-specific rules based on your project's tech stack
cp -r rules/typescript ~/.claude/rules/typescript
cp -r rules/python ~/.claude/rules/python
cp -r rules/golang ~/.claude/rules/golang
cp -r rules/swift ~/.claude/rules/swift
cp -r rules/php ~/.claude/rules/php
```

```bash
# 安装通用规则（所有项目都需要）
cp -r rules/common ~/.claude/rules/common

# 根据项目的技术栈安装语言特定规则
cp -r rules/typescript ~/.claude/rules/typescript
cp -r rules/python ~/.claude/rules/python
cp -r rules/golang ~/.claude/rules/golang
cp -r rules/swift ~/.claude/rules/swift
cp -r rules/php ~/.claude/rules/php
```

## Rules vs Skills
## 规则 vs 技能

- **Rules** define standards, conventions, and checklists that apply broadly (e.g., "80% test coverage", "no hardcoded secrets").
  **规则** 定义适用于广泛的标准、约定和检查清单（例如，"80% 测试覆盖率"、"无硬编码密钥"）。

- **Skills** (`skills/` directory) provide deep, actionable reference material for specific tasks (e.g., `python-patterns`, `golang-testing`).
  **技能**（`skills/` 目录）为特定任务提供深入、可操作的参考材料（例如 `python-patterns`、`golang-testing`）。

Language-specific rule files reference relevant skills where appropriate. Rules tell you *what* to do; skills tell you *how* to do it.
语言特定的规则文件在适当的地方引用相关技能。规则告诉你**做什么**；技能告诉你**怎么做**。

## Adding a New Language
## 添加新语言支持

To add support for a new language (e.g., `rust/`):
要添加新语言支持（例如 `rust/`）：

1. Create a `rules/rust/` directory
   创建 `rules/rust/` 目录

2. Add files that extend the common rules:
   添加扩展通用规则的文件：

   - `coding-style.md` — formatting tools, idioms, error handling patterns
     `coding-style.md` — 格式化工具、惯用法、错误处理模式

   - `testing.md` — test framework, coverage tools, test organization
     `testing.md` — 测试框架、覆盖率工具、测试组织

   - `patterns.md` — language-specific design patterns
     `patterns.md` — 语言特定的设计模式

   - `hooks.md` — PostToolUse hooks for formatters, linters, type checkers
     `hooks.md` — 格式化程序、linter、类型检查器的 PostToolUse 钩子

   - `security.md` — secret management, security scanning tools
     `security.md` — 密钥管理，安全扫描工具

3. Each file should start with:
   每个文件应以以下内容开头：

   ```
   > This file extends [common/xxx.md](../common/xxx.md) with <Language> specific content.
   ```

4. Reference existing skills if available, or create new ones under `skills/`.
   如果有现有技能请引用，或在 `skills/` 下创建新技能。

## Rule Priority
## 规则优先级

When language-specific rules and common rules conflict, **language-specific rules take precedence** (specific overrides general). This follows the standard layered configuration pattern (similar to CSS specificity or `.gitignore` precedence).
当语言特定规则与通用规则冲突时，**语言特定规则优先**（具体覆盖通用）。这遵循标准的分层配置模式（类似于 CSS 特异性或 `.gitignore` 优先级）。

- `rules/common/` defines universal defaults applicable to all projects.
  `rules/common/` 定义适用于所有项目的通用默认值。

- `rules/golang/`, `rules/python/`, `rules/swift/`, `rules/php/`, `rules/typescript/`, etc. override those defaults where language idioms differ.
  `rules/golang/`、`rules/python/`、`rules/swift/`、`rules/php/`、`rules/typescript/` 等在语言惯用法不同的领域覆盖这些默认值。

### Example
### 示例

`common/coding-style.md` recommends immutability as a default principle. A language-specific `golang/coding-style.md` can override this:
`common/coding-style.md` 推荐将不可变性作为默认原则。语言特定的 `golang/coding-style.md` 可以覆盖它：

> Idiomatic Go uses pointer receivers for struct mutation — see [common/coding-style.md](../common/coding-style.md) for the general principle, but Go-idiomatic mutation is preferred here.
> 惯用的 Go 使用指针接收器进行结构体变更 — 参见 [common/coding-style.md](../common/coding-style.md) 了解通用原则，但在此推荐 Go 惯用的变更方式。

### Common rules with override notes
### 带有覆盖说明的通用规则

Rules in `rules/common/` that may be overridden by language-specific files are marked with:
可能被语言特定文件覆盖的 `rules/common/` 中的规则会标注：

> **Language note**: This rule may be overridden by language-specific rules for languages where this pattern is not idiomatic.
> **语言说明**：此规则可能被语言特定规则覆盖，适用于该模式不是惯用做法的语言。
