# Contributing to Everything Claude Code
# 为 Everything Claude Code 做贡献

Thanks for wanting to contribute! This repo is a community resource for Claude Code users.
感谢您的贡献意愿！此仓库是 Claude Code 用户的社区资源。

## Table of Contents
## 目录

- [What We're Looking For](#what-were-looking-for)
- [Quick Start](#quick-start)
- [Contributing Skills](#contributing-skills)
- [Contributing Agents](#contributing-agents)
- [Contributing Hooks](#contributing-hooks)
- [Contributing Commands](#contributing-commands)
- [MCP and documentation (e.g. Context7)](#mcp-and-documentation-eg-context7)
- [Cross-Harness and Translations](#cross-harness-and-translations)
- [Pull Request Process](#pull-request-process)
- [我们需要什么](#我们需要什么)
- [快速开始](#快速开始)
- [贡献 Skills](#贡献-skills)
- [贡献 Agents](#贡献-agents)
- [贡献 Hooks](#贡献-hooks)
- [贡献 Commands](#贡献-commands)
- [MCP 和文档（如 Context7）](#mcp-和文档eg-context7)
- [跨 Harness 和翻译](#跨-harness-和翻译)
- [Pull Request 流程](#pull-request-流程)

---

## What We're Looking For
## 我们需要什么

### Agents
### Agents

New agents that handle specific tasks well:
处理特定任务的 new agents：
- **Language-specific reviewers** (Python, Go, Rust)
  **语言特定的审查者** (Python, Go, Rust)
- **Framework experts** (Django, Rails, Laravel, Spring)
  **框架专家** (Django, Rails, Laravel, Spring)
- **DevOps specialists** (Kubernetes, Terraform, CI/CD)
  **DevOps 专家** (Kubernetes, Terraform, CI/CD)
- **Domain experts** (ML pipelines, data engineering, mobile)
  **领域专家** (ML pipelines, data engineering, mobile)

### Skills
### Skills

Workflow definitions and domain knowledge:
工作流定义和领域知识：
- **Language best practices**
  **语言最佳实践**
- **Framework patterns**
  **框架模式**
- **Testing strategies**
  **测试策略**
- **Architecture guides**
  **架构指南**

### Hooks
### Hooks

Useful automations:
有用的自动化：
- **Linting/formatting hooks**
  **Linting/格式化 hooks**
- **Security checks**
  **安全检查**
- **Validation hooks**
  **验证 hooks**
- **Notification hooks**
  **通知 hooks**

### Commands
### Commands

Slash commands that invoke useful workflows:
调用有用工作流的斜杠命令：
- **Deployment commands**
  **部署命令**
- **Testing commands**
  **测试命令**
- **Code generation commands**
  **代码生成命令**

---

## Quick Start
## 快速开始

```bash
# 1. Fork and clone
gh repo fork affaan-m/everything-claude-code --clone
cd everything-claude-code
# 1. Fork 并克隆
gh repo fork affaan-m/everything-claude-code --clone
cd everything-claude-code

# 2. Create a branch
git checkout -b feat/my-contribution
# 2. 创建分支
git checkout -b feat/my-contribution

# 3. Add your contribution (see sections below)
# 3. 添加您的贡献（见下文）

# 4. Test locally
cp -r skills/my-skill ~/.claude/skills/  # for skills
# Then test with Claude Code
# 4. 本地测试
cp -r skills/my-skill ~/.claude/skills/  # 用于 skills
# 然后用 Claude Code 测试

# 5. Submit PR
git add . && git commit -m "feat: add my-skill" && git push -u origin feat/my-contribution
# 5. 提交 PR
git add . && git commit -m "feat: add my-skill" && git push -u origin feat/my-contribution
```

---

## Contributing Skills
## 贡献 Skills

Skills are knowledge modules that Claude Code loads based on context.
Skills 是 Claude Code 根据上下文加载的知识模块。

### Directory Structure
### 目录结构

```
skills/
└── your-skill-name/
    └── SKILL.md
```
```
skills/
└── your-skill-name/
    └── SKILL.md
```

### SKILL.md Template
### SKILL.md 模板

```markdown
---
name: your-skill-name
description: Brief description shown in skill list
origin: ECC
---

# Your Skill Title
# 中文标题

Brief overview of what this skill covers.
简要概述此 skill 涵盖的内容。

## Core Concepts
## 核心概念

Explain key patterns and guidelines.
解释关键模式和指南。

## Code Examples
## 代码示例

```typescript
// Include practical, tested examples
function example() {
  // Well-commented code
}
```
```typescript
// 包含实用的、经过测试的示例
function example() {
  // 良好注释的代码
}
```

## Best Practices
## 最佳实践

- Actionable guidelines
- 可操作的指南
- Do's and don'ts
- 应该做和不应该做的
- Common pitfalls to avoid
- 要避免的常见陷阱

## When to Use
## 使用时机

Describe scenarios where this skill applies.
描述此 skill 适用的场景。
```

### Skill Checklist
### Skill 检查清单

- [ ] Focused on one domain/technology
- [ ] 专注于一个领域/技术
- [ ] Includes practical code examples
- [ ] 包含实用的代码示例
- [ ] Under 500 lines
- [ ] 少于 500 行
- [ ] Uses clear section headers
- [ ] 使用清晰的部分标题
- [ ] Tested with Claude Code
- [ ] 用 Claude Code 测试过

### Example Skills
### 示例 Skills

| Skill | Purpose |
|-------|---------|
| `coding-standards/` | TypeScript/JavaScript patterns |
| `frontend-patterns/` | React and Next.js best practices |
| `backend-patterns/` | API and database patterns |
| `security-review/` | Security checklist |
| Skill | 用途 |
|-------|---------|
| `coding-standards/` | TypeScript/JavaScript 模式 |
| `frontend-patterns/` | React 和 Next.js 最佳实践 |
| `backend-patterns/` | API 和数据库模式 |
| `security-review/` | 安全检查清单 |

---

## Contributing Agents
## 贡献 Agents

Agents are specialized assistants invoked via the Task tool.
Agents 是通过 Task 工具调用的专业化助手。

### File Location
### 文件位置

```
agents/your-agent-name.md
```
```
agents/your-agent-name.md
```

### Agent Template
### Agent 模板

```markdown
---
name: your-agent-name
description: What this agent does and when Claude should invoke it. Be specific!
tools: ["Read", "Write", "Edit", "Bash", "Grep", "Glob"]
model: sonnet
---

# Your Role
# 你的角色

You are a [role] specialist.
你是 [角色] 专家。

## Your Role
## 你的角色

- Primary responsibility
- 主要职责
- Secondary responsibility
- 次要职责
- What you DO NOT do (boundaries)
- 你不做什么（边界）

## Workflow
## 工作流

### Step 1: Understand
### 步骤 1: 理解

How you approach the task.
你如何处理任务。

### Step 2: Execute
### 步骤 2: 执行

How you perform the work.
你如何完成工作。

### Step 3: Verify
### 步骤 3: 验证

How you validate results.
你如何验证结果。

## Output Format
## 输出格式

What you return to the user.
你返回给用户的内容。

## Examples
## 示例

### Example: [Scenario]
### 示例: [场景]

Input: [what user provides]
输入: [用户提供的]
Action: [what you do]
动作: [你做的]
Output: [what you return]
输出: [你返回的]
```

### Agent Fields
### Agent 字段

| Field | Description | Options |
|-------|-------------|---------|
| `name` | Lowercase, hyphenated | `code-reviewer` |
| `description` | Used to decide when to invoke | Be specific! |
| `tools` | Only what's needed | `Read, Write, Edit, Bash, Grep, Glob, WebFetch, Task`, or MCP tool names (e.g. `mcp__context7__resolve-library-id`, `mcp__context7__query-docs`) when the agent uses MCP |
| `model` | Complexity level | `haiku` (simple), `sonnet` (coding), `opus` (complex) |
| 字段 | 描述 | 选项 |
|------|------|------|
| `name` | 小写、带连字符 | `code-reviewer` |
| `description` | 用于决定何时调用 | 要具体！ |
| `tools` | 只需要用到的 | `Read, Write, Edit, Bash, Grep, Glob, WebFetch, Task`，或 MCP 工具名称（例如 agent 使用 MCP 时的 `mcp__context7__resolve-library-id`、`mcp__context7__query-docs`） |
| `model` | 复杂度级别 | `haiku`（简单）、`sonnet`（编码）、`opus`（复杂） |

### Example Agents
### 示例 Agents

| Agent | Purpose |
|-------|---------|
| `tdd-guide.md` | Test-driven development |
| `code-reviewer.md` | Code review |
| `security-reviewer.md` | Security scanning |
| `build-error-resolver.md` | Fix build errors |
| Agent | 用途 |
|-------|---------|
| `tdd-guide.md` | 测试驱动开发 |
| `code-reviewer.md` | 代码审查 |
| `security-reviewer.md` | 安全扫描 |
| `build-error-resolver.md` | 修复构建错误 |

---

## Contributing Hooks
## 贡献 Hooks

Hooks are automatic behaviors triggered by Claude Code events.
Hooks 是由 Claude Code 事件触发的自动行为。

### File Location
### 文件位置

```
hooks/hooks.json
```
```
hooks/hooks.json
```

### Hook Types
### Hook 类型

| Type | Trigger | Use Case |
|------|---------|----------|
| `PreToolUse` | Before tool runs | Validate, warn, block |
| `PostToolUse` | After tool runs | Format, check, notify |
| `SessionStart` | Session begins | Load context |
| `Stop` | Session ends | Cleanup, audit |
| 类型 | 触发条件 | 用途 |
|------|----------|------|
| `PreToolUse` | 工具运行前 | 验证、警告、阻止 |
| `PostToolUse` | 工具运行后 | 格式化、检查、通知 |
| `SessionStart` | 会话开始 | 加载上下文 |
| `Stop` | 会话结束 | 清理、审计 |

### Hook Format
### Hook 格式

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "tool == \"Bash\" && tool_input.command matches \"rm -rf /\"",
        "hooks": [
          {
            "type": "command",
            "command": "echo '[Hook] BLOCKED: Dangerous command' && exit 1"
          }
        ],
        "description": "Block dangerous rm commands"
      }
    ]
  }
}
```

### Matcher Syntax
### Matcher 语法

```javascript
// Match specific tools
tool == "Bash"
tool == "Edit"
tool == "Write"
// 匹配特定工具

// Match input patterns
tool_input.command matches "npm install"
tool_input.file_path matches "\\.tsx?$"
// 匹配输入模式

// Combine conditions
tool == "Bash" && tool_input.command matches "git push"
// 组合条件
```

### Hook Examples
### Hook 示例

```json
// Block dev servers outside tmux
{
  "matcher": "tool == \"Bash\" && tool_input.command matches \"npm run dev\"",
  "hooks": [{"type": "command", "command": "echo 'Use tmux for dev servers' && exit 1"}],
  "description": "Ensure dev servers run in tmux"
}
// 阻止在 tmux 外运行开发服务器

// Auto-format after editing TypeScript
{
  "matcher": "tool == \"Edit\" && tool_input.file_path matches \"\\.tsx?$\"",
  "hooks": [{"type": "command", "command": "npx prettier --write \"$file_path\""}],
  "description": "Format TypeScript files after edit"
}
// 编辑 TypeScript 后自动格式化

// Warn before git push
{
  "matcher": "tool == \"Bash\" && tool_input.command matches \"git push\"",
  "hooks": [{"type": "command", "command": "echo '[Hook] Review changes before pushing'"}],
  "description": "Reminder to review before push"
}
// git push 前警告
```

### Hook Checklist
### Hook 检查清单

- [ ] Matcher is specific (not overly broad)
- [ ] Matcher 精确（不太宽泛）
- [ ] Includes clear error/info messages
- [ ] 包含清晰的错误/信息消息
- [ ] Uses correct exit codes (`exit 1` blocks, `exit 0` allows)
- [ ] 使用正确的退出码（`exit 1` 阻止，`exit 0` 允许）
- [ ] Tested thoroughly
- [ ] 经过充分测试
- [ ] Has description
- [ ] 有描述

---

## Contributing Commands
## 贡献 Commands

Commands are user-invoked actions with `/command-name`.
Commands 是用户通过 `/command-name` 调用的操作。

### File Location
### 文件位置

```
commands/your-command.md
```
```
commands/your-command.md
```

### Command Template
### Command 模板

```markdown
---
description: Brief description shown in /help
---

# Command Name
# 命令名称

## Purpose
## 目的

What this command does.
此命令的作用。

## Usage
## 用法

```
/your-command [args]
```

## Workflow
## 工作流

1. First step
2. Second step
3. Final step
1. 第一步
2. 第二步
3. 最后一步

## Output
## 输出

What the user receives.
用户收到的内容。
```

### Example Commands
### 示例 Commands

| Command | Purpose |
|---------|---------|
| `commit.md` | Create git commits |
| `code-review.md` | Review code changes |
| `tdd.md` | TDD workflow |
| `e2e.md` | E2E testing |
| Command | 用途 |
|---------|---------|
| `commit.md` | 创建 git 提交 |
| `code-review.md` | 审查代码更改 |
| `tdd.md` | TDD 工作流 |
| `e2e.md` | E2E 测试 |

---

## MCP and documentation (e.g. Context7)
## MCP 和文档（如 Context7）

Skills and agents can use **MCP (Model Context Protocol)** tools to pull in up-to-date data instead of relying only on training data. This is especially useful for documentation.
Skills 和 agents 可以使用 **MCP（模型上下文协议）** 工具来拉取最新数据，而不是仅仅依赖训练数据。这对于文档尤其有用。

- **Context7** is an MCP server that exposes `resolve-library-id` and `query-docs`. Use it when the user asks about libraries, frameworks, or APIs so answers reflect current docs and code examples.
  **Context7** 是一个暴露 `resolve-library-id` 和 `query-docs` 的 MCP 服务器。当用户询问有关库、框架或 API 的问题时使用它，以便答案反映当前文档和代码示例。
- When contributing **skills** that depend on live docs (e.g. setup, API usage), describe how to use the relevant MCP tools (e.g. resolve the library ID, then query docs) and point to the `documentation-lookup` skill or Context7 as the pattern.
  贡献依赖于实时文档的 **skills**（例如设置、API 使用）时，描述如何使用相关的 MCP 工具（例如解析库 ID，然后查询文档），并指向 `documentation-lookup` skill 或 Context7 作为模式。
- When contributing **agents** that answer docs/API questions, include the Context7 MCP tool names (e.g. `mcp__context7__resolve-library-id`, `mcp__context7__query-docs`) in the agent's tools and document the resolve → query workflow.
  贡献回答文档/API 问题的 **agents** 时，在 agent 的 tools 中包含 Context7 MCP 工具名称（例如 `mcp__context7__resolve-library-id`、`mcp__context7__query-docs`），并记录解析 → 查询工作流。
- **mcp-configs/mcp-servers.json** includes a Context7 entry; users enable it in their harness (e.g. Claude Code, Cursor) to use the documentation-lookup skill (in `skills/documentation-lookup/`) and the `/docs` command.
  **mcp-configs/mcp-servers.json** 包含 Context7 条目；用户在他们的 harness（如 Claude Code、Cursor）中启用它，以使用 documentation-lookup skill（在 `skills/documentation-lookup/` 中）和 `/docs` 命令。

---

## Cross-Harness and Translations
## 跨 Harness 和翻译

### Skill subsets (Codex and Cursor)
### Skill 子集（Codex 和 Cursor）

ECC ships skill subsets for other harnesses:
ECC 为其他 harnesses 提供了 skill 子集：

- **Codex:** `.agents/skills/` — skills listed in `agents/openai.yaml` are loaded by Codex.
  **Codex:** `.agents/skills/` — 在 `agents/openai.yaml` 中列出的 skills 由 Codex 加载。
- **Cursor:** `.cursor/skills/` — a subset of skills is bundled for Cursor.
  **Cursor:** `.cursor/skills/` — 捆绑的 skills 子集用于 Cursor。

When you **add a new skill** that should be available on Codex or Cursor:
当您添加应该在 Codex 或 Cursor 上可用的 **新 skill** 时：

1. Add the skill under `skills/your-skill-name/` as usual.
1. 像往常一样在 `skills/your-skill-name/` 下添加 skill。
2. If it should be available on **Codex**, add it to `.agents/skills/` (copy the skill directory or add a reference) and ensure it is referenced in `agents/openai.yaml` if required.
2. 如果应该在 **Codex** 上可用，将其添加到 `.agents/skills/`（复制 skill 目录或添加引用），并在需要时确保它在 `agents/openai.yaml` 中被引用。
3. If it should be available on **Cursor**, add it under `.cursor/skills/` per Cursor's layout.
3. 如果应该在 **Cursor** 上可用，根据 Cursor 的布局将其添加到 `.cursor/skills/`。

Check existing skills in those directories for the expected structure. Keeping these subsets in sync is manual; mention in your PR if you updated them.
检查这些目录中的现有 skills 以了解预期的结构。保持这些子集同步是手动的；如果您更新了它们，请在 PR 中注明。

### Translations
### 翻译

Translations live under `docs/` (e.g. `docs/zh-CN`, `docs/zh-TW`, `docs/ja-JP`). If you change agents, commands, or skills that are translated, consider updating the corresponding translation files or opening an issue so maintainers or translators can update them.
翻译位于 `docs/` 下（例如 `docs/zh-CN`、`docs/zh-TW`、`docs/ja-JP`）。如果您更改了被翻译的 agents、commands 或 skills，请考虑更新相应的翻译文件或 opening 一个 issue，以便维护者或翻译人员可以更新它们。

---

## Pull Request Process
## Pull Request 流程

### 1. PR Title Format
### 1. PR 标题格式

```
feat(skills): add rust-patterns skill
feat(agents): add api-designer agent
feat(hooks): add auto-format hook
fix(skills): update React patterns
docs: improve contributing guide
```
```
feat(skills): add rust-patterns skill
feat(agents): add api-designer agent
feat(hooks): add auto-format hook
fix(skills): update React patterns
docs: improve contributing guide
```

### 2. PR Description
### 2. PR 描述

```markdown
## Summary
## 摘要

What you're adding and why.
你添加的内容及原因。

## Type
## 类型

- [ ] Skill
- [ ] Agent
- [ ] Hook
- [ ] Command
- [ ] Skill
- [ ] Agent
- [ ] Hook
- [ ] Command

## Testing
## 测试

How you tested this.
你如何测试的。

## Checklist
## 检查清单

- [ ] Follows format guidelines
- [ ] 遵循格式指南
- [ ] Tested with Claude Code
- [ ] 用 Claude Code 测试过
- [ ] No sensitive info (API keys, paths)
- [ ] 无敏感信息（API 密钥、路径）
- [ ] Clear descriptions
- [ ] 描述清晰
```

### 3. Review Process
### 3. 审查流程

1. Maintainers review within 48 hours
1. 维护者在 48 小时内审查
2. Address feedback if requested
2. 如有请求，解决反馈
3. Once approved, merged to main
3. 批准后，合并到 main

---

## Guidelines
## 指南

### Do
### 应该做

- Keep contributions focused and modular
- 保持贡献专注和模块化
- Include clear descriptions
- 包含清晰的描述
- Test before submitting
- 提交前测试
- Follow existing patterns
- 遵循现有模式
- Document dependencies
- 记录依赖

### Don't
### 不应该做

- Include sensitive data (API keys, tokens, paths)
- 包含敏感数据（API 密钥、令牌、路径）
- Add overly complex or niche configs
- 添加过于复杂或小众的配置
- Submit untested contributions
- 提交未经测试的贡献
- Create duplicates of existing functionality
- 创建现有功能的重复

---

## File Naming
## 文件命名

- Use lowercase with hyphens: `python-reviewer.md`
- 使用小写加连字符: `python-reviewer.md`
- Be descriptive: `tdd-workflow.md` not `workflow.md`
- 具有描述性: `tdd-workflow.md` 而不是 `workflow.md`
- Match name to filename
- 名称与文件名匹配

---

## Questions?
## 问题？

- **Issues:** [github.com/affaan-m/everything-claude-code/issues](https://github.com/affaan-m/everything-claude-code/issues)
  **Issues:** [github.com/affaan-m/everything-claude-code/issues](https://github.com/affaan-m/everything-claude-code/issues)
- **X/Twitter:** [@affaanmustafa](https://x.com/affaanmustafa)
  **X/Twitter:** [@affaanmustafa](https://x.com/affaanmustafa)

---

Thanks for contributing! Let's build a great resource together.
感谢您的贡献！让我们一起构建一个伟大的资源。
