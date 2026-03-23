---
name: autonomous-loops
description: "Patterns and architectures for autonomous Claude Code loops — from simple sequential pipelines to RFC-driven multi-agent DAG systems."
description zh-CN: 自主 Claude Code 循环的模式和架构——从简单的顺序管道到 RFC 驱动的多 agent DAG 系统。
origin: ECC
---

# Autonomous Loops Skill

# Autonomous Loops Skill
# 自主循环技能

> Compatibility note (v1.8.0): `autonomous-loops` is retained for one release.
> 兼容性说明（v1.8.0）：`autonomous-loops` 保留一个版本。
> The canonical skill name is now `continuous-agent-loop`. New loop guidance
> 规范技能名称现在是 `continuous-agent-loop`。新的循环指导
> should be authored there, while this skill remains available to avoid
> 应在那里编写，同时保留此技能以避免
> breaking existing workflows.
> 破坏现有工作流。

Patterns, architectures, and reference implementations for running Claude Code autonomously in loops. Covers everything from simple `claude -p` pipelines to full RFC-driven multi-agent DAG orchestration.
运行 Claude Code 自主循环的模式、架构和参考实现。涵盖从简单的 `claude -p` 管道到完整的 RFC 驱动的多 agent DAG 编排。

## When to Use

## When to Use
## 何时使用

- Setting up autonomous development workflows that run without human intervention
  - 设置无需人工干预的自主开发工作流
- Choosing the right loop architecture for your problem (simple vs complex)
  - 为你的问题选择正确的循环架构（简单 vs 复杂）
- Building CI/CD-style continuous development pipelines
  - 构建 CI/CD 风格的持续开发管道
- Running parallel agents with merge coordination
  - 运行具有合并协调的并行 agent
- Implementing context persistence across loop iterations
  - 在循环迭代之间实现上下文持久化
- Adding quality gates and cleanup passes to autonomous workflows
  - 为自主工作流添加质量门和清理关卡

## Loop Pattern Spectrum

## Loop Pattern Spectrum
## 循环模式谱系

From simplest to most sophisticated:
从最简单到最复杂：

| Pattern | Complexity | Best For |
|---------|-----------|----------|
| [Sequential Pipeline](#1-sequential-pipeline-claude--p) | Low | Daily dev steps, scripted workflows |
| [NanoClaw REPL](#2-nanoclaw-repl) | Low | Interactive persistent sessions |
| [Infinite Agentic Loop](#3-infinite-agentic-loop) | Medium | Parallel content generation, spec-driven work |
| [Continuous Claude PR Loop](#4-continuous-claude-pr-loop) | Medium | Multi-day iterative projects with CI gates |
| [De-Sloppify Pattern](#5-the-de-sloppify-pattern) | Add-on | Quality cleanup after any Implementer step |
| [Ralphinho / RFC-Driven DAG](#6-ralphinho--rfc-driven-dag-orchestration) | High | Large features, multi-unit parallel work with merge queue |
|---|---|---|
| 模式 | 复杂度 | 最佳场景 |
| [顺序管道](#1-sequential-pipeline-claude--p) | 低 | 日常开发步骤、脚本化工作流 |
| [NanoClaw REPL](#2-nanoclaw-repl) | 低 | 交互式持久会话 |
| [无限代理循环](#3-infinite-agentic-loop) | 中 | 并行内容生成、规范驱动的工作 |
| [持续 Claude PR 循环](#4-continuous-claude-pr-loop) | 中 | 多日迭代项目与 CI 关卡 |
| [De-Sloppify 模式](#5-the-de-sloppify-pattern) | 附加组件 | 在任何实现者步骤后进行质量清理 |
| [Ralphinho / RFC 驱动的 DAG](#6-ralphinho--rfc-driven-dag-orchestration) | 高 | 大型功能、多单元并行工作与合并队列 |

---

## 1. Sequential Pipeline (`claude -p`)

## 1. Sequential Pipeline (`claude -p`)
## 1. 顺序管道（`claude -p`）

**The simplest loop.** Break daily development into a sequence of non-interactive `claude -p` calls. Each call is a focused step with a clear prompt.
**最简单的循环。** 将日常开发分解为一系列非交互式的 `claude -p` 调用。每次调用是一个带有清晰提示的专注步骤。

### Core Insight

### Core Insight
### 核心洞察

> If you can't figure out a loop like this, it means you can't even drive the LLM to fix your code in interactive mode.
> 如果你连这样的循环都搞不定，意味着你在交互模式下甚至无法驱动 LLM 来修复你的代码。

The `claude -p` flag runs Claude Code non-interactively with a prompt, exits when done. Chain calls to build a pipeline:
`claude -p` 标志以提示运行 Claude Code 非交互式运行，完成后退出。链式调用以构建管道：

```bash
#!/bin/bash
# daily-dev.sh — Sequential pipeline for a feature branch
# daily-dev.sh — 功能分支的顺序管道

set -e

# Step 1: Implement the feature
# 步骤 1：实现功能
claude -p "Read the spec in docs/auth-spec.md. Implement OAuth2 login in src/auth/. Write tests first (TDD). Do NOT create any new documentation files."

# Step 2: De-sloppify (cleanup pass)
# 步骤 2：De-sloppify（清理关卡）
claude -p "Review all files changed by the previous commit. Remove any unnecessary type tests, overly defensive checks, or testing of language features (e.g., testing that TypeScript generics work). Keep real business logic tests. Run the test suite after cleanup."

# Step 3: Verify
# 步骤 3：验证
claude -p "Run the full build, lint, type check, and test suite. Fix any failures. Do not add new features."

# Step 4: Commit
# 步骤 4：提交
claude -p "Create a conventional commit for all staged changes. Use 'feat: add OAuth2 login flow' as the message."
```

### Key Design Principles

### Key Design Principles
### 关键设计原则

1. **Each step is isolated** — A fresh context window per `claude -p` call means no context bleed between steps.
  1. **每个步骤隔离**——每次 `claude -p` 调用都有全新的上下文窗口，步骤之间无上下文渗透。
2. **Order matters** — Steps execute sequentially. Each builds on the filesystem state left by the previous.
  2. **顺序很重要**——步骤按顺序执行。每个步骤都基于前一个步骤留下的文件系统状态。
3. **Negative instructions are dangerous** — Don't say "don't test type systems." Instead, add a separate cleanup step (see [De-Sloppify Pattern](#5-the-de-sloppify-pattern)).
  3. **负面指令很危险**——不要说"不要测试类型系统"。而是添加一个单独的清理步骤（参见 [De-Sloppify 模式](#5-the-de-sloppify-pattern)）。
4. **Exit codes propagate** — `set -e` stops the pipeline on failure.
  4. **退出码会传播**——`set -e` 在失败时停止管道。

### Variations

### Variations
### 变体

**With model routing:**
**使用模型路由：**
```bash
# Research with Opus (deep reasoning)
# 使用 Opus 研究（深度推理）
claude -p --model opus "Analyze the codebase architecture and write a plan for adding caching..."

# Implement with Sonnet (fast, capable)
# 使用 Sonnet 实现（快速、有能力）
claude -p "Implement the caching layer according to the plan in docs/caching-plan.md..."

# Review with Opus (thorough)
# 使用 Opus 审查（彻底）
claude -p --model opus "Review all changes for security issues, race conditions, and edge cases..."
```

**With environment context:**
**使用环境上下文：**
```bash
# Pass context via files, not prompt length
# 通过文件传递上下文，而非提示长度
echo "Focus areas: auth module, API rate limiting" > .claude-context.md
claude -p "Read .claude-context.md for priorities. Work through them in order."
rm .claude-context.md
```

**With `--allowedTools` restrictions:**
**使用 `--allowedTools` 限制：**
```bash
# Read-only analysis pass
# 只读分析关卡
claude -p --allowedTools "Read,Grep,Glob" "Audit this codebase for security vulnerabilities..."

# Write-only implementation pass
# 只写实现关卡
claude -p --allowedTools "Read,Write,Edit,Bash" "Implement the fixes from security-audit.md..."
```

---

## 2. NanoClaw REPL

## 2. NanoClaw REPL
## 2. NanoClaw REPL

**ECC's built-in persistent loop.** A session-aware REPL that calls `claude -p` synchronously with full conversation history.
**ECC 内置的持久循环。** 一个会话感知的 REPL，以完整的对话历史同步调用 `claude -p`。

```bash
# Start the default session
# 启动默认会话
node scripts/claw.js

# Named session with skill context
# 带技能上下文的命名会话
CLAW_SESSION=my-project CLAW_SKILLS=tdd-workflow,security-review node scripts/claw.js
```

### How It Works

### How It Works
### 工作原理

1. Loads conversation history from `~/.claude/claw/{session}.md`
  1. 从 `~/.claude/claw/{session}.md` 加载对话历史
2. Each user message is sent to `claude -p` with full history as context
  2. 每条用户消息以完整历史作为上下文发送到 `claude -p`
3. Responses are appended to the session file (Markdown-as-database)
  3. 响应追加到会话文件（Markdown 即数据库）
4. Sessions persist across restarts
  4. 会话在重启后持久化

### When NanoClaw vs Sequential Pipeline

### When NanoClaw vs Sequential Pipeline
### NanoClaw vs 顺序管道何时使用

| Use Case | NanoClaw | Sequential Pipeline |
|----------|----------|-------------------|
| Interactive exploration | Yes | No |
| Scripted automation | No | Yes |
| Session persistence | Built-in | Manual |
| Context accumulation | Grows per turn | Fresh each step |
| CI/CD integration | Poor | Excellent |
|---|---|---|
| 使用场景 | NanoClaw | 顺序管道 |
| 交互式探索 | 是 | 否 |
| 脚本化自动化 | 否 | 是 |
| 会话持久化 | 内置 | 手动 |
| 上下文累积 | 每轮增长 | 每步全新 |
| CI/CD 集成 | 差 | 优秀 |

See the `/claw` command documentation for full details.
有关完整详细信息，请参见 `/claw` 命令文档。

---

## 3. Infinite Agentic Loop

## 3. Infinite Agentic Loop
## 3. 无限代理循环

**A two-prompt system** that orchestrates parallel sub-agents for specification-driven generation. Developed by disler (credit: @disler).
**一个双提示系统**，为规范驱动的生成编排并行子 agent。由 disler 开发（来源：@disler）。

### Architecture: Two-Prompt System

### Architecture: Two-Prompt System
### 架构：双提示系统

```
PROMPT 1 (Orchestrator)              PROMPT 2 (Sub-Agents)
提示 1（编排器）                    提示 2（子代理）
┌─────────────────────┐             ┌──────────────────────┐
│ Parse spec file      │             │ Receive full context  │
│ 解析规范文件           │             │ 接收完整上下文          │
│ Scan output dir      │  deploys   │ Read assigned number  │
│ 扫描输出目录           │  部署      │ 读取分配的编号           │
│ Plan iteration       │────────────│ Follow spec exactly   │
│ 规划迭代              │   N agents │ 严格遵循规范            │
│ Assign creative dirs │             │ Generate unique output │
│ 分配创意方向          │             │ 生成独特输出            │
│ Manage waves         │             │ Save to output dir    │
│ 管理波次              │             │ 保存到输出目录          │
└─────────────────────┘             └──────────────────────┘
```

### The Pattern

### The Pattern
### 模式

1. **Spec Analysis** — Orchestrator reads a specification file (Markdown) defining what to generate
  1. **规范分析**——编排器读取定义要生成内容的规范文件（Markdown）
2. **Directory Recon** — Scans existing output to find the highest iteration number
  2. **目录侦察**——扫描现有输出以找到最高的迭代编号
3. **Parallel Deployment** — Launches N sub-agents, each with:
  3. **并行部署**——启动 N 个子 agent，每个都有：
   - The full spec
   - 完整的规范
   - A unique creative direction
   - 独特的创意方向
   - A specific iteration number (no conflicts)
   - 特定的迭代编号（无冲突）
   - A snapshot of existing iterations (for uniqueness)
   - 现有迭代的快照（确保唯一性）
4. **Wave Management** — For infinite mode, deploys waves of 3-5 agents until context is exhausted
  4. **波次管理**——对于无限模式，部署 3-5 个 agent 的波次直到上下文耗尽

### Implementation via Claude Code Commands

### Implementation via Claude Code Commands
### 通过 Claude Code 命令实现

Create `.claude/commands/infinite.md`:
创建 `.claude/commands/infinite.md`：

```markdown
Parse the following arguments from $ARGUMENTS:
从 $ARGUMENTS 解析以下参数：
1. spec_file — path to the specification markdown
1. spec_file — 规范 markdown 的路径
2. output_dir — where iterations are saved
2. output_dir — 迭代保存的位置
3. count — integer 1-N or "infinite"
3. count — 整数 1-N 或 "infinite"

PHASE 1: Read and deeply understand the specification.
PHASE 1: 阅读并深入理解规范。
PHASE 2: List output_dir, find highest iteration number. Start at N+1.
PHASE 2: 列出 output_dir，找到最高迭代编号。从 N+1 开始。
PHASE 3: Plan creative directions — each agent gets a DIFFERENT theme/approach.
PHASE 3: 规划创意方向——每个 agent 得到一个不同的主题/方法。
PHASE 4: Deploy sub-agents in parallel (Task tool). Each receives:
PHASE 4: 并行部署子 agent（Task 工具）。每个接收：
  - Full spec text
  - 完整规范文本
  - Current directory snapshot
  - 当前目录快照
  - Their assigned iteration number
  - 分配的迭代编号
  - Their unique creative direction
  - 独特的创意方向
PHASE 5 (infinite mode): Loop in waves of 3-5 until context is low.
PHASE 5（无限模式）：以 3-5 波次循环直到上下文偏低。
```

**Invoke:**
**调用：**
```bash
/project:infinite specs/component-spec.md src/ 5
/project:infinite specs/component-spec.md src/ infinite
```

### Batching Strategy

### Batching Strategy
### 批处理策略

| Count | Strategy |
|-------|----------|
| 1-5 | All agents simultaneously |
| 6-20 | Batches of 5 |
| infinite | Waves of 3-5, progressive sophistication |
|---|---|---|
| 数量 | 策略 |
| 1-5 | 所有 agent 同时 |
| 6-20 | 每批 5 个 |
| infinite | 3-5 波次，逐步深化 |

### Key Insight: Uniqueness via Assignment

### Key Insight: Uniqueness via Assignment
### 关键洞察：通过分配实现唯一性

Don't rely on agents to self-differentiate. The orchestrator **assigns** each agent a specific creative direction and iteration number. This prevents duplicate concepts across parallel agents.
不要依赖 agent 自我区分。编排器**分配**每个 agent 特定的创意方向和迭代编号。这可以防止并行 agent 之间的重复概念。

---

## 4. Continuous Claude PR Loop

## 4. Continuous Claude PR Loop
## 4. 持续 Claude PR 循环

**A production-grade shell script** that runs Claude Code in a continuous loop, creating PRs, waiting for CI, and merging automatically. Created by AnandChowdhary (credit: @AnandChowdhary).
**一个生产级 shell 脚本**，以连续循环运行 Claude Code，创建 PR、等待 CI 并自动合并。由 AnandChowdhary 创建（来源：@AnandChowdhary）。

### Core Loop

### Core Loop
### 核心循环

```
┌─────────────────────────────────────────────────────┐
│  CONTINUOUS CLAUDE ITERATION                        │
│  持续 CLAUDE 迭代                                    │
│                                                     │
│  1. Create branch (continuous-claude/iteration-N)   │
│  2. Run claude -p with enhanced prompt              │
│  3. (Optional) Reviewer pass — separate claude -p   │
│  4. Commit changes (claude generates message)       │
│  5. Push + create PR (gh pr create)                 │
│  6. Wait for CI checks (poll gh pr checks)          │
│  7. CI failure? → Auto-fix pass (claude -p)         │
│  8. Merge PR (squash/merge/rebase)                 │
│  9. Return to main → repeat                        │
│                                                     │
│  Limit by: --max-runs N | --max-cost $X             │
│            --max-duration 2h | completion signal     │
└─────────────────────────────────────────────────────┘
```

### Installation

### Installation
### 安装

```bash
curl -fsSL https://raw.githubusercontent.com/AnandChowdhary/continuous-claude/HEAD/install.sh | bash
```

### Usage

### Usage
### 使用

```bash
# Basic: 10 iterations
# 基本：10 次迭代
continuous-claude --prompt "Add unit tests for all untested functions" --max-runs 10

# Cost-limited
# 成本限制
continuous-claude --prompt "Fix all linter errors" --max-cost 5.00

# Time-boxed
# 时间限制
continuous-claude --prompt "Improve test coverage" --max-duration 8h

# With code review pass
# 带代码审查关卡
continuous-claude \
  --prompt "Add authentication feature" \
  --max-runs 10 \
  --review-prompt "Run npm test && npm run lint, fix any failures"

# Parallel via worktrees
# 通过 worktree 并行
continuous-claude --prompt "Add tests" --max-runs 5 --worktree tests-worker &
continuous-claude --prompt "Refactor code" --max-runs 5 --worktree refactor-worker &
wait
```

### Cross-Iteration Context: SHARED_TASK_NOTES.md

### Cross-Iteration Context: SHARED_TASK_NOTES.md
### 跨迭代上下文：SHARED_TASK_NOTES.md

The critical innovation: a `SHARED_TASK_NOTES.md` file persists across iterations:
关键创新：一个 `SHARED_TASK_NOTES.md` 文件在迭代之间持久化：

```markdown
## Progress
## 进展
- [x] Added tests for auth module (iteration 1)
- [x] Fixed edge case in token refresh (iteration 2)
- [ ] Still need: rate limiting tests, error boundary tests
- [ ] 仍需：限流测试、错误边界测试

## Next Steps
## 下一步
- Focus on rate limiting module next
- 下一步关注限流模块
- The mock setup in tests/helpers.ts can be reused
- tests/helpers.ts 中的 mock 设置可以复用
```

Claude reads this file at iteration start and updates it at iteration end. This bridges the context gap between independent `claude -p` invocations.
Claude 在迭代开始时读取此文件，在迭代结束时更新它。这弥合了独立 `claude -p` 调用之间的上下文差距。

### CI Failure Recovery

### CI Failure Recovery
### CI 失败恢复

When PR checks fail, Continuous Claude automatically:
当 PR 检查失败时，持续 Claude 自动：
1. Fetches the failed run ID via `gh run list`
  1. 通过 `gh run list` 获取失败的运行 ID
2. Spawns a new `claude -p` with CI fix context
  2. 用 CI 修复上下文生成新的 `claude -p`
3. Claude inspects logs via `gh run view`, fixes code, commits, pushes
  3. Claude 通过 `gh run view` 检查日志，修复代码，提交，推送
4. Re-waits for checks (up to `--ci-retry-max` attempts)
  4. 重新等待检查（最多 `--ci-retry-max` 次尝试）

### Completion Signal

### Completion Signal
### 完成信号

Claude can signal "I'm done" by outputting a magic phrase:
Claude 可以通过输出一个魔法短语来发出"我完成了"的信号：

```bash
continuous-claude \
  --prompt "Fix all bugs in the issue tracker" \
  --completion-signal "CONTINUOUS_CLAUDE_PROJECT_COMPLETE" \
  --completion-threshold 3  # Stops after 3 consecutive signals
```

Three consecutive iterations signaling completion stops the loop, preventing wasted runs on finished work.
连续三次迭代发出完成信号会停止循环，防止在已完成的工作上浪费运行。

### Key Configuration

### Key Configuration
### 关键配置

| Flag | Purpose |
|------|---------|
| `--max-runs N` | Stop after N successful iterations |
| `--max-cost $X` | Stop after spending $X |
| `--max-duration 2h` | Stop after time elapsed |
| `--merge-strategy squash` | squash, merge, or rebase |
| `--worktree <name>` | Parallel execution via git worktrees |
| `--disable-commits` | Dry-run mode (no git operations) |
| `--review-prompt "..."` | Add reviewer pass per iteration |
| `--ci-retry-max N` | Auto-fix CI failures (default: 1) |
|---|---|---|
| 标志 | 用途 |
| `--max-runs N` | N 次成功迭代后停止 |
| `--max-cost $X` | 花费 $X 后停止 |
| `--max-duration 2h` | 经过时间后停止 |
| `--merge-strategy squash` | squash、merge 或 rebase |
| `--worktree <name>` | 通过 git worktree 并行执行 |
| `--disable-commits` | 干运行模式（无 git 操作）|
| `--review-prompt "..."` | 每次迭代添加审查关卡 |
| `--ci-retry-max N` | 自动修复 CI 失败（默认：1）|

---

## 5. The De-Sloppify Pattern

## 5. The De-Sloppify Pattern
## 5. De-Sloppify 模式

**An add-on pattern for any loop.** Add a dedicated cleanup/refactor step after each Implementer step.
**任何循环的附加模式。** 在每个实现者步骤后添加专门的清理/重构步骤。

### The Problem

### The Problem
### 问题

When you ask an LLM to implement with TDD, it takes "write tests" too literally:
当你要求 LLM 用 TDD 实现时，它会过于字面地理解"编写测试"：
- Tests that verify TypeScript's type system works (testing `typeof x === 'string'`)
  - 验证 TypeScript 类型系统工作的测试（测试 `typeof x === 'string'`）
- Overly defensive runtime checks for things the type system already guarantees
  - 对类型系统已保证的内容过度防御的运行时检查
- Tests for framework behavior rather than business logic
  - 测试框架行为而非业务逻辑
- Excessive error handling that obscures the actual code
  - 过度错误处理掩盖了实际代码

### Why Not Negative Instructions?

### Why Not Negative Instructions?
### 为什么不用负面指令？

Adding "don't test type systems" or "don't add unnecessary checks" to the Implementer prompt has downstream effects:
在实现者提示中添加"不要测试类型系统"或"不要添加不必要的检查"有下游影响：
- The model becomes hesitant about ALL testing
  - 模型对所有测试变得犹豫
- It skips legitimate edge case tests
  - 它跳过合法的边界情况测试
- Quality degrades unpredictably
  - 质量不可预测地下降

### The Solution: Separate Pass

### The Solution: Separate Pass
### 解决方案：单独关卡

Instead of constraining the Implementer, let it be thorough. Then add a focused cleanup agent:
不要约束实现者，让它彻底。然后添加一个专注的清理 agent：

```bash
# Step 1: Implement (let it be thorough)
# 步骤 1：实现（让它彻底）
claude -p "Implement the feature with full TDD. Be thorough with tests."

# Step 2: De-sloppify (separate context, focused cleanup)
# 步骤 2：De-sloppify（单独的上下文，专注的清理）
claude -p "Review all changes in the working tree. Remove:
- Tests that verify language/framework behavior rather than business logic
- Redundant type checks that the type system already enforces
- Over-defensive error handling for impossible states
- Console.log statements
- Commented-out code

Keep all business logic tests. Run the test suite after cleanup to ensure nothing breaks."
```

### In a Loop Context

### In a Loop Context
### 在循环上下文中

```bash
for feature in "${features[@]}"; do
  # Implement
  # 实现
  claude -p "Implement $feature with TDD."

  # De-sloppify
  # De-sloppify
  claude -p "Cleanup pass: review changes, remove test/code slop, run tests."

  # Verify
  # 验证
  claude -p "Run build + lint + tests. Fix any failures."

  # Commit
  # 提交
  claude -p "Commit with message: feat: add $feature"
done
```

### Key Insight

### Key Insight
### 关键洞察

> Rather than adding negative instructions which have downstream quality effects, add a separate de-sloppify pass. Two focused agents outperform one constrained agent.
> 与其添加有下游质量影响的负面指令，不如添加一个单独的 de-sloppify 关卡。两个专注的 agent 优于一个受限的 agent。

---

## 6. Ralphinho / RFC-Driven DAG Orchestration

## 6. Ralphinho / RFC-Driven DAG Orchestration
## 6. Ralphinho / RFC 驱动的 DAG 编排

**The most sophisticated pattern.** An RFC-driven, multi-agent pipeline that decomposes a spec into a dependency DAG, runs each unit through a tiered quality pipeline, and lands them via an agent-driven merge queue. Created by enitrat (credit: @enitrat).
**最复杂的模式。** 一个 RFC 驱动的多 agent 管道，将规范分解为依赖 DAG，通过分级质量管道运行每个单元，并通过 agent 驱动的合并队列落地。由 enitrat 创建（来源：@enitrat）。

### Architecture Overview

### Architecture Overview
### 架构概述

```
RFC/PRD Document
RFC/PRD 文档
       │
       ▼
  DECOMPOSITION (AI)
  分解（AI）
  Break RFC into work units with dependency DAG
  将 RFC 分解为带依赖 DAG 的工作单元
       │
       ▼
┌──────────────────────────────────────────────────────┐
│  RALPH LOOP (up to 3 passes)                         │
│  RALPH 循环（最多 3 次）                             │
│                                                      │
│  For each DAG layer (sequential, by dependency):     │
│  对于每个 DAG 层（按依赖顺序）：                      │
│                                                      │
│  ┌── Quality Pipelines (parallel per unit) ───────┐  │
│  │  每个单元在其自己的工作树中：                    │  │
│  │  Research → Plan → Implement → Test → Review   │  │
│  │  研究 → 规划 → 实现 → 测试 → 审查               │  │
│  │  (depth varies by complexity tier)             │  │
│  │  （深度因复杂度层级而异）                        │  │
│  └────────────────────────────────────────────────┘  │
│                                                      │
│  ┌── Merge Queue ─────────────────────────────────┐  │
│  │  Rebase onto main → Run tests → Land or evict │  │
│  │  重新基于 main → 运行测试 → 落地或驱逐          │  │
│  │  Evicted units re-enter with conflict context  │  │
│  └────────────────────────────────────────────────┘  │
│                                                      │
└──────────────────────────────────────────────────────┘
```

### RFC Decomposition

### RFC Decomposition
### RFC 分解

AI reads the RFC and produces work units:
AI 读取 RFC 并生成工作单元：

```typescript
interface WorkUnit {
  id: string;              // kebab-case identifier
  name: string;            // Human-readable name
  rfcSections: string[];   // Which RFC sections this addresses
  description: string;     // Detailed description
  deps: string[];          // Dependencies (other unit IDs)
  acceptance: string[];    // Concrete acceptance criteria
  tier: "trivial" | "small" | "medium" | "large";
}
```

**Decomposition Rules:**
**分解规则：**
- Prefer fewer, cohesive units (minimize merge risk)
  - 偏好更少、更有凝聚力的单元（最小化合并风险）
- Minimize cross-unit file overlap (avoid conflicts)
  - 最小化跨单元文件重叠（避免冲突）
- Keep tests WITH implementation (never separate "implement X" + "test X")
  - 将测试与实现放在一起（绝不分开"实现 X"+"测试 X"）
- Dependencies only where real code dependency exists
  - 仅在存在真实代码依赖时设置依赖

The dependency DAG determines execution order:
依赖 DAG 决定执行顺序：
```
Layer 0: [unit-a, unit-b]     ← no deps, run in parallel
Layer 1: [unit-c]             ← depends on unit-a
Layer 2: [unit-d, unit-e]     ← depend on unit-c
第 0 层：[unit-a, unit-b]     ← 无依赖，并行运行
第 1 层：[unit-c]             ← 依赖 unit-a
第 2 层：[unit-d, unit-e]     ← 依赖 unit-c
```

### Complexity Tiers

### Complexity Tiers
### 复杂度层级

Different tiers get different pipeline depths:
不同的层级获得不同的管道深度：

| Tier | Pipeline Stages |
|------|----------------|
| **trivial** | implement → test |
| **small** | implement → test → code-review |
| **medium** | research → plan → implement → test → PRD-review + code-review → review-fix |
| **large** | research → plan → implement → test → PRD-review + code-review → review-fix → final-review |
|---|---|---|
| 层级 | 管道阶段 |
| **trivial** | 实现 → 测试 |
| **small** | 实现 → 测试 → 代码审查 |
| **medium** | 研究 → 规划 → 实现 → 测试 → PRD 审查 + 代码审查 → 审查修复 |
| **large** | 研究 → 规划 → 实现 → 测试 → PRD 审查 + 代码审查 → 审查修复 → 最终审查 |

This prevents expensive operations on simple changes while ensuring architectural changes get thorough scrutiny.
这防止了简单变更的昂贵操作，同时确保架构变更得到彻底审查。

### Separate Context Windows (Author-Bias Elimination)

### Separate Context Windows (Author-Bias Elimination)
### 独立上下文窗口（消除作者偏见）

Each stage runs in its own agent process with its own context window:
每个阶段在其自己的 agent 进程中运行，拥有自己的上下文窗口：

| Stage | Model | Purpose |
|-------|-------|---------|
| Research | Sonnet | Read codebase + RFC, produce context doc |
| Plan | Opus | Design implementation steps |
| Implement | Codex | Write code following the plan |
| Test | Sonnet | Run build + test suite |
| PRD Review | Sonnet | Spec compliance check |
| Code Review | Opus | Quality + security check |
| Review Fix | Codex | Address review issues |
| Final Review | Opus | Quality gate (large tier only) |
|---|---|---|
| 阶段 | 模型 | 用途 |
| 研究 | Sonnet | 读取代码库 + RFC，生成上下文文档 |
| 规划 | Opus | 设计实现步骤 |
| 实现 | Codex | 按计划编写代码 |
| 测试 | Sonnet | 运行构建 + 测试套件 |
| PRD 审查 | Sonnet | 规范合规检查 |
| 代码审查 | Opus | 质量 + 安全检查 |
| 审查修复 | Codex | 解决审查问题 |
| 最终审查 | Opus | 质量门（仅 large 层级）|

**Critical design:** The reviewer never wrote the code it reviews. This eliminates author bias — the most common source of missed issues in self-review.
**关键设计：** 审查者从不审查自己写的代码。这消除了作者偏见——自我审查中最常见的遗漏问题来源。

### Merge Queue with Eviction

### Merge Queue with Eviction
### 带驱逐的合并队列

After quality pipelines complete, units enter the merge queue:
质量管道完成后，单元进入合并队列：

```
Unit branch
单元分支
    │
    ├─ Rebase onto main
    │   └─ Conflict? → EVICT (capture conflict context)
    │   └─ 冲突？→ 驱逐（捕获冲突上下文）
    │
    ├─ Run build + tests
    │   └─ Fail? → EVICT (capture test output)
    │   └─ 失败？→ 驱逐（捕获测试输出）
    │
    └─ Pass → Fast-forward main, push, delete branch
    └─ 通过 → 快进 main，推送，删除分支
```

**File Overlap Intelligence:**
**文件重叠智能：**
- Non-overlapping units land speculatively in parallel
  - 无重叠单元并行推测性落地
- Overlapping units land one-by-one, rebasing each time
  - 有重叠单元逐一落地，每次重新基于

**Eviction Recovery:**
**驱逐恢复：**
When evicted, full context is captured (conflicting files, diffs, test output) and fed back to the implementer on the next Ralph pass:
当被驱逐时，完整的上下文被捕获（冲突文件、diff、测试输出），并在下一个 Ralph 循环中反馈给实现者：

```markdown
## MERGE CONFLICT — RESOLVE BEFORE NEXT LANDING
## 合并冲突——在下一次落地前解决

Your previous implementation conflicted with another unit that landed first.
你的上一个实现与先落地的另一个单元冲突了。
Restructure your changes to avoid the conflicting files/lines below.
重构你的更改以避免以下冲突的文件/行。

{full eviction context with diffs}
{带有 diff 的完整驱逐上下文}
```

### Data Flow Between Stages

### Data Flow Between Stages
### 阶段间数据流

```
research.contextFilePath ──────────────────→ plan
plan.implementationSteps ──────────────────→ implement
implement.{filesCreated, whatWasDone} ─────→ test, reviews
test.failingSummary ───────────────────────→ reviews, implement (next pass)
reviews.{feedback, issues} ────────────────→ review-fix → implement (next pass)
final-review.reasoning ────────────────────→ implement (next pass)
evictionContext ───────────────────────────→ implement (after merge conflict)
```

### Worktree Isolation

### Worktree Isolation
### 工作树隔离

Every unit runs in an isolated worktree (uses jj/Jujutsu, not git):
每个单元在隔离的工作树中运行（使用 jj/Jujutsu，而非 git）：
```
/tmp/workflow-wt-{unit-id}/
```

Pipeline stages for the same unit **share** a worktree, preserving state (context files, plan files, code changes) across research → plan → implement → test → review.
同一单元的管道阶段**共享**一个工作树，在研究 → 规划 → 实现 → 测试 → 审查之间保留状态（上下文文件、计划文件、代码更改）。

### Key Design Principles

### Key Design Principles
### 关键设计原则

1. **Deterministic execution** — Upfront decomposition locks in parallelism and ordering
  1. **确定性执行**——预先分解锁定并行和顺序
2. **Human review at leverage points** — The work plan is the single highest-leverage intervention point
  2. **在杠杆点进行人工审查**——工作计划是单一最高杠杆干预点
3. **Separate concerns** — Each stage in a separate context window with a separate agent
  3. **分离关注点**——每个阶段在独立的上下文窗口中由独立的 agent 处理
4. **Conflict recovery with context** — Full eviction context enables intelligent re-runs, not blind retries
  4. **带上下文的冲突恢复**——完整的驱逐上下文支持智能重新运行，而非盲目重试
5. **Tier-driven depth** — Trivial changes skip research/review; large changes get maximum scrutiny
  5. **层级驱动深度**——trivial 变更跳过研究/审查；large 变更获得最大程度的审查
6. **Resumable workflows** — Full state persisted to SQLite; resume from any point
  6. **可恢复的工作流**——完整状态持久化到 SQLite；从任何点恢复

### When to Use Ralphinho vs Simpler Patterns

### When to Use Ralphinho vs Simpler Patterns
### 何时使用 Ralphinho vs 更简单的模式

| Signal | Use Ralphinho | Use Simpler Pattern |
|--------|--------------|-------------------|
| Multiple interdependent work units | Yes | No |
| Need parallel implementation | Yes | No |
| Merge conflicts likely | Yes | No (sequential is fine) |
| Single-file change | No | Yes (sequential pipeline) |
| Multi-day project | Yes | Maybe (continuous-claude) |
| Spec/RFC already written | Yes | Maybe |
| Quick iteration on one thing | No | Yes (NanoClaw or pipeline) |
|---|---|---|
| 信号 | 使用 Ralphinho | 使用更简单的模式 |
| 多个相互依赖的工作单元 | 是 | 否 |
| 需要并行实现 | 是 | 否 |
| 可能出现合并冲突 | 是 | 否（顺序即可）|
| 单文件更改 | 否 | 是（顺序管道）|
| 多日项目 | 是 | 也许（持续 Claude）|
| 规范/RFC 已编写 | 是 | 也许 |
| 快速迭代一件事 | 否 | 是（NanoClaw 或管道）|

---

## Choosing the Right Pattern

## Choosing the Right Pattern
## 选择正确的模式

### Decision Matrix

### Decision Matrix
### 决策矩阵

```
Is the task a single focused change?
├─ Yes → Sequential Pipeline or NanoClaw
└─ No → Is there a written spec/RFC?
         ├─ Yes → Do you need parallel implementation?
         │        ├─ Yes → Ralphinho (DAG orchestration)
         │        └─ No → Continuous Claude (iterative PR loop)
         └─ No → Do you need many variations of the same thing?
                  ├─ Yes → Infinite Agentic Loop (spec-driven generation)
                  └─ No → Sequential Pipeline with de-sloppify
任务是一个单一的专注变更吗？
├─ 是 → 顺序管道或 NanoClaw
└─ 否 → 有书面规范/RFC 吗？
         ├─ 是 → 需要并行实现吗？
         │        ├─ 是 → Ralphinho（DAG 编排）
         │        └─ 否 → 持续 Claude（迭代 PR 循环）
         └─ 否 → 需要同一事物的许多变体吗？
                  ├─ 是 → 无限代理循环（规范驱动的生成）
                  └─ 否 → 带 de-sloppify 的顺序管道
```

### Combining Patterns

### Combining Patterns
### 组合模式

These patterns compose well:
这些模式可以很好地组合：

1. **Sequential Pipeline + De-Sloppify** — The most common combination. Every implement step gets a cleanup pass.
  1. **顺序管道 + De-Sloppify**——最常见的组合。每个实现步骤都获得一个清理关卡。

2. **Continuous Claude + De-Sloppify** — Add `--review-prompt` with a de-sloppify directive to each iteration.
  2. **持续 Claude + De-Sloppify**——在每次迭代中添加带有 de-sloppify 指令的 `--review-prompt`。

3. **Any loop + Verification** — Use ECC's `/verify` command or `verification-loop` skill as a gate before commits.
  3. **任何循环 + 验证**——在提交前使用 ECC 的 `/verify` 命令或 `verification-loop` 技能作为关卡。

4. **Ralphinho's tiered approach in simpler loops** — Even in a sequential pipeline, you can route simple tasks to Haiku and complex tasks to Opus:
  4. **Ralphinho 的分级方法用于更简单的循环**——即使在顺序管道中，你也可以将简单任务路由到 Haiku，将复杂任务路由到 Opus：
   ```bash
   # Simple formatting fix
   # 简单格式修复
   claude -p --model haiku "Fix the import ordering in src/utils.ts"

   # Complex architectural change
   # 复杂的架构变更
   claude -p --model opus "Refactor the auth module to use the strategy pattern"
   ```

---

## Anti-Patterns

## Anti-Patterns
## 反模式

### Common Mistakes

### Common Mistakes
### 常见错误

1. **Infinite loops without exit conditions** — Always have a max-runs, max-cost, max-duration, or completion signal.
  1. **无限循环没有退出条件**——始终设置 max-runs、max-cost、max-duration 或完成信号。

2. **No context bridge between iterations** — Each `claude -p` call starts fresh. Use `SHARED_TASK_NOTES.md` or filesystem state to bridge context.
  2. **迭代之间没有上下文桥接**——每次 `claude -p` 调用都是全新的。使用 `SHARED_TASK_NOTES.md` 或文件系统状态来桥接上下文。

3. **Retrying the same failure** — If an iteration fails, don't just retry. Capture the error context and feed it to the next attempt.
  3. **重试相同的失败**——如果迭代失败，不要只是重试。捕获错误上下文并将其提供给下一次尝试。

4. **Negative instructions instead of cleanup passes** — Don't say "don't do X." Add a separate pass that removes X.
  4. **用负面指令代替清理关卡**——不要说"不要做 X"。添加一个移除 X 的单独关卡。

5. **All agents in one context window** — For complex workflows, separate concerns into different agent processes. The reviewer should never be the author.
  5. **所有 agent 在一个上下文窗口中**——对于复杂工作流，将关注点分离到不同的 agent 进程中。审查者不应是作者。

6. **Ignoring file overlap in parallel work** — If two parallel agents might edit the same file, you need a merge strategy (sequential landing, rebase, or conflict resolution).
  6. **忽略并行工作中的文件重叠**——如果两个并行 agent 可能编辑同一文件，你需要合并策略（顺序落地、重新基于或冲突解决）。

---

## References

## References
## 参考资料

| Project | Author | Link |
|---------|--------|------|
| Ralphinho | enitrat | credit: @enitrat |
| Infinite Agentic Loop | disler | credit: @disler |
| Continuous Claude | AnandChowdhary | credit: @AnandChowdhary |
| NanoClaw | ECC | `/claw` command in this repo |
| Verification Loop | ECC | `skills/verification-loop/` in this repo |
|---|---|---|
| 项目 | 作者 | 链接 |
| Ralphinho | enitrat | 来源：@enitrat |
| Infinite Agentic Loop | disler | 来源：@disler |
| Continuous Claude | AnandChowdhary | 来源：@AnandChowdhary |
| NanoClaw | ECC | 本仓库中的 `/claw` 命令 |
| Verification Loop | ECC | 本仓库中的 `skills/verification-loop/` |
