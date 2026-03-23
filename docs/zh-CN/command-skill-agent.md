---
name: command-skill-agent
description: Command、Skill、Agent 三者的本质区别与实战选择
---

# Command vs Skill vs Agent — 本质区别与实战选择

> 本文档基于 Claude Code 官方文档和项目实际代码编写。

## 一句话总结

| 类型 | 本质 | 触发方式 |
|------|------|----------|
| **Command** | 可通过斜杠命令执行的 Skill | `/命令名` 显式触发 |
| **Skill** | 领域知识与工作流定义 | prompt 描述匹配后自动加载 |
| **Agent** | 独立任务执行者 | `Agent` tool 显式启动 |

> **官方说法**："命令是通过斜杠命令执行的技能"。但从实际文件格式看，两者有明显区别——Skill 是 Command 的升级版，支持更丰富的元数据和自动匹配。

---

## 1. Command — 可快速执行的 Skill

```
文件位置：~/.claude/commands/*.md 或项目的 commands/*.md
frontmatter：仅 description
触发方式：/命令名 显式触发
```

**本质**：Command 就是 Skill，只是更轻量——frontmatter 只有 `description`，没有 `name`、`tools`、`origin` 等字段，专为简单快捷的任务设计。

**格式示例**（项目 `commands/tdd.md`）：
```yaml
---
description: Enforce test-driven development workflow. Scaffold interfaces,
  generate tests FIRST, then implement minimal code to pass.
---

# TDD Command

This command invokes the **tdd-guide** agent...

## When to Use
Use `/tdd` when:
- Implementing new features
- Fixing bugs
...
```

**关键点**：
- frontmatter **只有 `description`**，无 `name`、`tools`、`model`
- 内容是一份**详细的工作流指南**
- 末尾通常注明 `This command invokes the {agent-name} agent`

### 项目中的 Command

| Command | 调用的 Agent | 作用 |
|---------|-------------|------|
| `/tdd` | `tdd-guide` | 测试驱动开发 |
| `/plan` | `planner` | 实施规划 |
| `/code-review` | `code-reviewer` | 代码审查 |
| `/e2e` | `e2e-runner` | 端到端测试 |

---

## 2. Skill — 领域知识与工作流定义

```
文件位置：~/.claude/skills/{name}/SKILL.md 或项目的 skills/{name}/SKILL.md
frontmatter：name, description, origin, tools（可选）
触发方式：prompt 描述自动匹配
```

**本质**：当用户描述的任务与 skill 的 `description` 匹配时，Claude Code 自动加载对应的 SKILL.md 文件。

**格式示例**（项目 `skills/tdd-workflow/SKILL.md`）：
```yaml
---
name: tdd-workflow
description: Use this skill when writing new features, fixing bugs,
  or refactoring code. Enforces test-driven development...
origin: ECC
---

# Test-Driven Development Workflow

This skill ensures all code development follows TDD principles...

## When to Activate
- Writing new features or functionality
- Fixing bugs or issues
...

## Core Principles
...
```

**两种类型**：
- **纯知识型**：无 `tools`，仅提供领域知识（参考）
- **可执行型**：有 `tools`，可直接执行任务

**与 Command 的关键区别**：
- Command = **显式调用**（`/tdd`），frontmatter 极简
- Skill = **自动加载**（描述匹配时自动加载），frontmatter 更丰富

> Skill 是 Command 的**升级版**——增加了 `name`、`origin`、`tools` 等字段，支持 prompt 模糊匹配，功能更强大。

---

## 3. Agent — 独立任务执行者

```
文件位置：~/.claude/agents/*.md 或项目的 agents/*.md
frontmatter：name, description, tools, model
触发方式：Agent tool 显式启动
```

**本质**：被 `Agent` tool 显式调用的独立执行者，拥有明确的工具权限和模型配置，运行在隔离的上下文中。

**格式示例**（项目 `agents/tdd-guide.md`）：
```yaml
---
name: tdd-guide
description: Test-Driven Development specialist enforcing write-tests-first...
tools: ["Read", "Write", "Edit", "Bash", "Grep"]
model: sonnet
---

You are a Test-Driven Development (TDD) specialist...

## Your Role
- Enforce tests-before-code methodology
- Guide through Red-Green-Refactor cycle
...
```

**关键字段**：
- `tools`：显式声明可用工具（`["Read", "Write", "Edit", "Bash"]`）
- `model`：指定使用的模型（`sonnet` / `haiku` / `opus`）
- 上下文**完全隔离**，不影响主会话

**Agent vs Skill/Command 的本质区别**：
- Skill/Command 是**知识库和工作流定义**——描述"怎么做"
- Agent 是**独立执行者**——真正去"做"

---

## 4. 三者关系图

```
用户输入：/tdd
       ↓
commands/tdd.md 加载（仅 description frontmatter）
       ↓
"This command invokes the tdd-guide agent"
       ↓
Agent tool 启动 tdd-guide
       ↓
agents/tdd-guide.md 加载（name + tools + model）
       ↓
可能同时加载 skills/tdd-workflow/SKILL.md（知识补充）
```

```
用户描述："我需要用测试驱动开发来实现这个功能"
       ↓
Claude Code 自动匹配 skill description
       ↓
skills/tdd-workflow/SKILL.md 加载（When to Activate）
       ↓
同时启动 Agent tool → tdd-guide agent
```

---

## 5. 全景对比表

| 维度 | Command | Skill | Agent |
|:---|:---|:---|:---|
| **本质** | 轻量快捷入口 | 升级版工作流定义 | 独立执行者 |
| **与 Skill 的关系** | Skill 的简化版本 | Command 的升级版 | 独立于 Skill/Command |
| **文件位置** | `~/.claude/commands/` | `~/.claude/skills/` | `~/.claude/agents/` |
| **YAML frontmatter** | 仅 `description` | `name`, `description`, `origin`, `tools`（可选） | `name`, `description`, `tools`, `model` |
| **触发方式** | `/命令名` | prompt 描述自动匹配 | `Agent` tool 显式调用 |
| **上下文** | 共享主会话 | 共享主会话 | **完全隔离** |
| **工具声明** | ❌ 无 | ✅ 可选 | ✅ 显式声明 |
| **模型指定** | ❌ 无 | ❌ 无 | ✅ 支持 |
| **并行能力** | ❌ 串行 | ❌ 串行 | ✅ 并行 |
| **成本** | 🟢 低 | 🟢 低 | 🔴 高（启动开销） |
| **内容** | 详细工作流指南 | 领域知识结构 | 角色定义 + 执行约束 |

---

## 6. 决策树：选哪个？

```
任务需要显式快捷触发（/xxx）？
  └─ YES → Command
  └─ NO ↓
    需要 Claude 自动理解并加载？
      └─ YES → Skill
      └─ NO ↓
        需要并行处理/隔离执行？
          ├─ YES → Agent
          └─ NO → Skill
```

### 实战对照

| 场景 | 推荐 | 理由 |
|------|------|------|
| "我需要 TDD 开发一个新功能" | **Skill** | Claude 自动匹配 `tdd-workflow` |
| 直接执行 `/tdd` | **Command** | 显式触发简单快捷任务 |
| 需要并行审查 50 个文件 | **Agent** | 隔离执行，不阻塞主对话 |
| 日常代码审查，可随时打断讨论 | **Command → Agent** | Command 触发 → Agent 执行 |
| 提供 Go 代码风格知识 | **Skill** | 自动加载 `golang-patterns` |
| 需要指定使用 sonnet 模型执行复杂任务 | **Agent** | 显式指定 model 和 tools |

---

## 7. 总结

> **Command = 轻量的 Skill 快捷入口**（`/xxx` 触发，frontmatter 极简）
> **Skill = 升级版工作流定义**（prompt 匹配触发，frontmatter 丰富）
> **Agent = 独立执行者**（`Agent` tool 触发，上下文隔离、显式工具和模型）

**对于日常开发**：
- 简单快捷任务 → Command（`/tdd`、`/plan`）
- 需要自动匹配/复杂工作流 → Skill（自动加载）
- 需要并行/隔离/指定模型 → Agent（显式调用）

**项目建议**：
- Command 作为简单任务的快捷入口
- Skill 作为复杂工作流的主定义（Command 可引用 Skill）
- Agent 作为实际执行者（拥有工具和模型配置）
