---
name: blueprint
description: >-
  Turn a one-line objective into a step-by-step construction plan for
  multi-session, multi-agent engineering projects. Each step has a
  self-contained context brief so a fresh agent can execute it cold.
  Includes adversarial review gate, dependency graph, parallel step
  detection, anti-pattern catalog, and plan mutation protocol.
  TRIGGER when: user requests a plan, blueprint, or roadmap for a
  complex multi-PR task, or describes work that needs multiple sessions.
  DO NOT TRIGGER when: task is completable in a single PR or fewer
  than 3 tool calls, or user says "just do it".
description zh-CN: >-
  将一句话目标转化为多会话、多 agent 工程项目的分步构建计划。
  每个步骤都有自包含的上下文简报，以便新的 agent 可以冷启动执行。
  包含对抗性审查关卡、依赖图、并行步骤检测、反模式目录和计划变更协议。
  触发条件：用户请求复杂多 PR 任务或需要多个会话的工作的计划、蓝图或路线图。
  不触发条件：任务可以在单个 PR 或少于 3 次工具调用中完成，或用户说"直接做"。
origin: community
---

# Blueprint — Construction Plan Generator

# Blueprint — Construction Plan Generator
# Blueprint——构建计划生成器

Turn a one-line objective into a step-by-step construction plan that any coding agent can execute cold.
将一句话目标转化为任何编码 agent 都可以冷启动执行的分步构建计划。

## When to Use

## When to Use
## 何时使用

- Breaking a large feature into multiple PRs with clear dependency order
  - 将大型功能分解为多个具有明确依赖顺序的 PR
- Planning a refactor or migration that spans multiple sessions
  - 规划跨越多个会话的重构或迁移
- Coordinating parallel workstreams across sub-agents
  - 协调跨子 agent 的并行工作流
- Any task where context loss between sessions would cause rework
  - 任何会话之间上下文丢失会导致返工的任务

**Do not use** for tasks completable in a single PR, fewer than 3 tool calls, or when the user says "just do it."
**不要使用**于可在单个 PR 中完成的任务、少于 3 次工具调用，或当用户说"直接做"时。

## How It Works

## How It Works
## 工作原理

Blueprint runs a 5-phase pipeline:
Blueprint 运行 5 阶段管道：

1. **Research** — Pre-flight checks (git, gh auth, remote, default branch), then reads project structure, existing plans, and memory files to gather context.
  1. **研究**——预检（git、gh auth、remote、默认分支），然后读取项目结构、现有计划和内存文件以收集上下文。
2. **Design** — Breaks the objective into one-PR-sized steps (3–12 typical). Assigns dependency edges, parallel/serial ordering, model tier (strongest vs default), and rollback strategy per step.
  2. **设计**——将目标分解为单个 PR 大小的步骤（通常 3-12 个）。为每个步骤分配依赖边、并行/串行排序、模型层级（最强 vs 默认）和回滚策略。
3. **Draft** — Writes a self-contained Markdown plan file to `plans/`. Every step includes a context brief, task list, verification commands, and exit criteria — so a fresh agent can execute any step without reading prior steps.
  3. **起草**——将自包含的 Markdown 计划文件写入 `plans/`。每个步骤都包含上下文简报、任务列表、验证命令和退出标准——这样新的 agent 可以执行任何步骤而无需阅读先前的步骤。
4. **Review** — Delegates adversarial review to a strongest-model sub-agent (e.g., Opus) against a checklist and anti-pattern catalog. Fixes all critical findings before finalizing.
  4. **审查**——将对抗性审查委托给最强模型子 agent（例如 Opus），对照检查清单和反模式目录进行审查。在最终确定前修复所有关键发现。
5. **Register** — Saves the plan, updates memory index, and presents the step count and parallelism summary to the user.
  5. **注册**——保存计划、更新内存索引，并向用户展示步骤数量和并行性摘要。

Blueprint detects git/gh availability automatically. With git + GitHub CLI, it generates full branch/PR/CI workflow plans. Without them, it switches to direct mode (edit-in-place, no branches).
Blueprint 自动检测 git/gh 的可用性。使用 git + GitHub CLI 时，它生成完整的 branch/PR/CI 工作流计划。没有它们时，切换到直接模式（就地编辑，无分支）。

## Examples

## Examples
## 示例

### Basic usage

### Basic usage
### 基本用法

```
/blueprint myapp "migrate database to PostgreSQL"
```

Produces `plans/myapp-migrate-database-to-postgresql.md` with steps like:
生成 `plans/myapp-migrate-database-to-postgresql.md`，包含类似以下的步骤：
- Step 1: Add PostgreSQL driver and connection config
  - 步骤 1：添加 PostgreSQL 驱动和连接配置
- Step 2: Create migration scripts for each table
  - 步骤 2：为每个表创建迁移脚本
- Step 3: Update repository layer to use new driver
  - 步骤 3：更新 repository 层以使用新驱动
- Step 4: Add integration tests against PostgreSQL
  - 步骤 4：添加针对 PostgreSQL 的集成测试
- Step 5: Remove old database code and config
  - 步骤 5：移除旧的数据库代码和配置

### Multi-agent project

### Multi-agent project
### 多 agent 项目

```
/blueprint chatbot "extract LLM providers into a plugin system"
```

Produces a plan with parallel steps where possible (e.g., "implement Anthropic plugin" and "implement OpenAI plugin" run in parallel after the plugin interface step is done), model tier assignments (strongest for the interface design step, default for implementation), and invariants verified after every step (e.g., "all existing tests pass", "no provider imports in core").
生成一个具有并行步骤的计划（可能的情况下，例如"实现 Anthropic 插件"和"实现 OpenAI 插件"在插件接口步骤完成后并行运行），模型层级分配（接口设计步骤使用最强模型，实现使用默认模型），以及每个步骤后验证的不变量（例如"所有现有测试通过"、"核心中没有 provider 导入"）。

## Key Features

## Key Features
## 关键特性

- **Cold-start execution** — Every step includes a self-contained context brief. No prior context needed.
  - **冷启动执行**——每个步骤都包含自包含的上下文简报。无需先前上下文。
- **Adversarial review gate** — Every plan is reviewed by a strongest-model sub-agent against a checklist covering completeness, dependency correctness, and anti-pattern detection.
  - **对抗性审查关卡**——每个计划都由最强模型子 agent 审查，对照涵盖完整性、依赖正确性和反模式检测的检查清单。
- **Branch/PR/CI workflow** — Built into every step. Degrades gracefully to direct mode when git/gh is absent.
  - **Branch/PR/CI 工作流**——内置于每个步骤。当 git/gh 缺失时优雅降级到直接模式。
- **Parallel step detection** — Dependency graph identifies steps with no shared files or output dependencies.
  - **并行步骤检测**——依赖图识别没有共享文件或输出依赖的步骤。
- **Plan mutation protocol** — Steps can be split, inserted, skipped, reordered, or abandoned with formal protocols and audit trail.
  - **计划变更协议**——步骤可以通过正式协议和审计跟踪进行拆分、插入、跳过、重新排序或放弃。
- **Zero runtime risk** — Pure Markdown skill. The entire repository contains only `.md` files — no hooks, no shell scripts, no executable code, no `package.json`, no build step. Nothing runs on install or invocation beyond Claude Code's native Markdown skill loader.
  - **零运行时风险**——纯 Markdown 技能。整个仓库只包含 `.md` 文件——无钩子、无 shell 脚本、无可执行代码、无 `package.json`、无构建步骤。在安装或调用时除了 Claude Code 原生的 Markdown 技能加载器外什么都不运行。

## Installation

## Installation
## 安装

This skill ships with Everything Claude Code. No separate installation is needed when ECC is installed.
此技能随 Everything Claude Code 一起提供。安装 ECC 时无需单独安装。

### Full ECC install

### Full ECC install
### 完整 ECC 安装

If you are working from the ECC repository checkout, verify the skill is present with:
如果你从 ECC 仓库检出工作，使用以下命令验证技能是否存在：

```bash
test -f skills/blueprint/SKILL.md
```

To update later, review the ECC diff before updating:
稍后更新时，在更新前审查 ECC diff：

```bash
cd /path/to/everything-claude-code
git fetch origin main
git log --oneline HEAD..origin/main       # review new commits before updating
git log --oneline HEAD..origin/main       # 在更新前审查新提交
git checkout <reviewed-full-sha>          # pin to a specific reviewed commit
git checkout <reviewed-full-sha>          # 固定到特定的已审查提交
```

### Vendored standalone install

### Vendored standalone install
### 独立 vendored 安装

If you are vendoring only this skill outside the full ECC install, copy the reviewed file from the ECC repository into `~/.claude/skills/blueprint/SKILL.md`. Vendored copies do not have a git remote, so update them by re-copying the file from a reviewed ECC commit rather than running `git pull`.
如果你只在完整 ECC 安装之外 vendored 此技能，将已审查的文件从 ECC 仓库复制到 `~/.claude/skills/blueprint/SKILL.md`。Vendored 副本没有 git remote，因此通过从已审查的 ECC 提交重新复制文件来更新，而不是运行 `git pull`。

## Requirements

## Requirements
## 要求

- Claude Code (for `/blueprint` slash command)
  - Claude Code（用于 `/blueprint` 斜杠命令）
- Git + GitHub CLI (optional — enables full branch/PR/CI workflow; Blueprint detects absence and auto-switches to direct mode)
  - Git + GitHub CLI（可选——启用完整的 branch/PR/CI 工作流；Blueprint 检测到不存在时自动切换到直接模式）

## Source

## Source
## 来源

Inspired by antbotlab/blueprint — upstream project and reference design.
灵感来自 antbotlab/blueprint——上游项目和参考设计。
