---
name: dmux-workflows
description: Multi-agent orchestration using dmux (tmux pane manager for AI agents). Patterns for parallel agent workflows across Claude Code, Codex, OpenCode, and other harnesses. Use when running multiple agent sessions in parallel or coordinating multi-agent development workflows.
description zh-CN: 使用 dmux（AI 代理的 tmux 窗格管理器）进行多代理编排。跨 Claude Code、Codex、OpenCode 和其他 harness 的并行代理工作流模式。在并行运行多个代理会话或协调多代理开发工作流时使用。
origin: ECC
---

# dmux Workflows

## dmux Workflows
## dmux 工作流

Orchestrate parallel AI agent sessions using dmux, a tmux pane manager for agent harnesses.
使用 dmux（AI 代理的 tmux 窗格管理器）编排并行 AI 代理会话。

## When to Activate

## When to Activate
## 何时激活

- Running multiple agent sessions in parallel
  并行运行多个代理会话
- Coordinating work across Claude Code, Codex, and other harnesses
  跨 Claude Code、Codex 和其他 harness 协调工作
- Complex tasks that benefit from divide-and-conquer parallelism
  从分治并行中受益的复杂任务
- User says "run in parallel", "split this work", "use dmux", or "multi-agent"
  用户说"并行运行"、"拆分这项工作"、"使用 dmux"或"多代理"

## What is dmux

## What is dmux
## 什么是 dmux

dmux is a tmux-based orchestration tool that manages AI agent panes:
dmux 是一个基于 tmux 的编排工具，用于管理 AI 代理窗格：

- Press `n` to create a new pane with a prompt
  按 `n` 创建带提示的新窗格
- Press `m` to merge pane output back to the main session
  按 `m` 将窗格输出合并回主会话
- Supports: Claude Code, Codex, OpenCode, Cline, Gemini, Qwen
  支持：Claude Code、Codex、OpenCode、Cline、Gemini、Qwen

**Install:** `npm install -g dmux` or see [github.com/standardagents/dmux](https://github.com/standardagents/dmux)
**安装：** `npm install -g dmux` 或参见 [github.com/standardagents/dmux](https://github.com/standardagents/dmux)

## Quick Start

## Quick Start
## 快速开始

```bash
# Start dmux session
# 启动 dmux 会话
dmux

# Create agent panes (press 'n' in dmux, then type prompt)
# 创建代理窗格（在 dmux 中按 'n'，然后输入提示）
# Pane 1: "Implement the auth middleware in src/auth/"
# 窗格 1："在 src/auth/ 中实现 auth 中间件"
# Pane 2: "Write tests for the user service"
# 窗格 2："为用户服务编写测试"
# Pane 3: "Update API documentation"
# 窗格 3："更新 API 文档"

# Each pane runs its own agent session
# 每个窗格运行自己的代理会话
# Press 'm' to merge results back
# 按 'm' 合并结果
```

## Workflow Patterns

## Workflow Patterns
## 工作流模式

### Pattern 1: Research + Implement

### Pattern 1: Research + Implement
### 模式 1：研究 + 实现

Split research and implementation into parallel tracks:
将研究和实现拆分为并行轨道：

```
Pane 1 (Research): "Research best practices for rate limiting in Node.js.
  Check current libraries, compare approaches, and write findings to
  /tmp/rate-limit-research.md"

Pane 1（研究）："研究 Node.js 速率限制的最佳实践。
  检查当前库，比较方法，并将发现写入
  /tmp/rate-limit-research.md"

Pane 2 (Implement): "Implement rate limiting middleware for our Express API.
  Start with a basic token bucket, we'll refine after research completes."

Pane 2（实现）："为我们的 Express API 实现速率限制中间件。
  从基本的令牌桶开始，研究完成后我们将进行改进。"

# After Pane 1 completes, merge findings into Pane 2's context
# Pane 1 完成后，将发现合并到 Pane 2 的上下文中
```

### Pattern 2: Multi-File Feature

### Pattern 2: Multi-File Feature
### 模式 2：多文件功能

Parallelize work across independent files:
跨独立文件并行工作：

```
Pane 1: "Create the database schema and migrations for the billing feature"
Pane 1："为计费功能创建数据库模式和迁移"
Pane 2: "Build the billing API endpoints in src/api/billing/"
Pane 2："在 src/api/billing/ 中构建计费 API 端点"
Pane 3: "Create the billing dashboard UI components"
Pane 3："创建计费仪表板 UI 组件"

# Merge all, then do integration in main pane
# 合并所有，然后在主窗格中进行集成
```

### Pattern 3: Test + Fix Loop

### Pattern 3: Test + Fix Loop
### 模式 3：测试 + 修复循环

Run tests in one pane, fix in another:
在一个窗格中运行测试，在另一个窗格中修复：

```
Pane 1 (Watcher): "Run the test suite in watch mode. When tests fail,
  summarize the failures."

Pane 1（观察者）："在监视模式下运行测试套件。当测试失败时，
  总结失败情况。"

Pane 2 (Fixer): "Fix failing tests based on the error output from pane 1"
Pane 2（修复者）："根据窗格 1 的错误输出修复失败的测试"
```

### Pattern 4: Cross-Harness

### Pattern 4: Cross-Harness
### 模式 4：跨 Harness

Use different AI tools for different tasks:
对不同的任务使用不同的 AI 工具：

```
Pane 1 (Claude Code): "Review the security of the auth module"
Pane 1（Claude Code）："审查 auth 模块的安全性"
Pane 2 (Codex): "Refactor the utility functions for performance"
Pane 2（Codex）："重构工具函数以提高性能"
Pane 3 (Claude Code): "Write E2E tests for the checkout flow"
Pane 3（Claude Code）："为结账流程编写 E2E 测试"
```

### Pattern 5: Code Review Pipeline

### Pattern 5: Code Review Pipeline
### 模式 5：代码审查流水线

Parallel review perspectives:
并行审查视角：

```
Pane 1: "Review src/api/ for security vulnerabilities"
Pane 1："审查 src/api/ 的安全漏洞"
Pane 2: "Review src/api/ for performance issues"
Pane 2："审查 src/api/ 的性能问题"
Pane 3: "Review src/api/ for test coverage gaps"
Pane 3："审查 src/api/ 的测试覆盖缺口"

# Merge all reviews into a single report
# 将所有审查合并为一份报告
```

## Best Practices

## Best Practices
## 最佳实践

1. **Independent tasks only.** Don't parallelize tasks that depend on each other's output.
   **仅独立任务。**不要并行化相互依赖输出的任务。
2. **Clear boundaries.** Each pane should work on distinct files or concerns.
   **清晰的边界。** 每个窗格应处理不同的文件或关注点。
3. **Merge strategically.** Review pane output before merging to avoid conflicts.
   **战略性合并。** 合并前审查窗格输出以避免冲突。
4. **Use git worktrees.** For file-conflict-prone work, use separate worktrees per pane.
   **使用 git worktrees。** 对于容易产生文件冲突的工作，为每个窗格使用单独的 worktree。
5. **Resource awareness.** Each pane uses API tokens — keep total panes under 5-6.
   **资源意识。** 每个窗格使用 API 令牌——保持总数在 5-6 个以下。

## Git Worktree Integration

## Git Worktree Integration
## Git Worktree 集成

For tasks that touch overlapping files:
对于涉及重叠文件的任务：

```bash
# Create worktrees for isolation
# 创建 worktree 以实现隔离
git worktree add -b feat/auth ../feature-auth HEAD
git worktree add -b feat/billing ../feature-billing HEAD

# Run agents in separate worktrees
# 在单独的 worktree 中运行代理
# Pane 1: cd ../feature-auth && claude
# Pane 2: cd ../feature-billing && claude

# Merge branches when done
# 完成后合并分支
git merge feat/auth
git merge feat/billing
```

## Complementary Tools

## Complementary Tools
## 补充工具

| Tool | What It Does | When to Use |
|------|-------------|-------------|
| **dmux** | tmux pane management for agents | Parallel agent sessions |
| **Superset** | Terminal IDE for 10+ parallel agents | Large-scale orchestration |
| **Claude Code Task tool** | In-process subagent spawning | Programmatic parallelism within a session |
| **Codex multi-agent** | Built-in agent roles | Codex-specific parallel work |

| 工具 | 功能 | 使用场景 |
|------|------|---------|
| **dmux** | 代理的 tmux 窗格管理 | 并行代理会话 |
| **Superset** | 10+ 并行代理的终端 IDE | 大规模编排 |
| **Claude Code Task 工具** | 进程内子代理生成 | 会话内的编程并行化 |
| **Codex 多代理** | 内置代理角色 | Codex 特定的并行工作 |

## ECC Helper

## ECC Helper
## ECC 助手

ECC now includes a helper for external tmux-pane orchestration with separate git worktrees:
ECC 现在包含一个助手，用于外部 tmux 窗格编排与单独的 git worktree：

```bash
node scripts/orchestrate-worktrees.js plan.json --execute
```

Example `plan.json`:
示例 `plan.json`：

```json
{
  "sessionName": "skill-audit",
  "baseRef": "HEAD",
  "launcherCommand": "codex exec --cwd {worktree_path} --task-file {task_file}",
  "workers": [
    { "name": "docs-a", "task": "Fix skills 1-4 and write handoff notes." },
    { "name": "docs-b", "task": "Fix skills 5-8 and write handoff notes." }
  ]
}
```

The helper:
该助手：

- Creates one branch-backed git worktree per worker
  为每个 worker 创建一个基于分支的 git worktree
- Optionally overlays selected `seedPaths` from the main checkout into each worker worktree
  可选择将主 checkout 中选定的 `seedPaths` 覆盖到每个 worker worktree
- Writes per-worker `task.md`, `handoff.md`, and `status.md` files under `.orchestration/<session>/`
  在 `.orchestration/<session>/` 下编写每个 worker 的 `task.md`、`handoff.md` 和 `status.md` 文件
- Starts a tmux session with one pane per worker
  启动一个 tmux 会话，每个 worker 一个窗格
- Launches each worker command in its own pane
  在自己的窗格中启动每个 worker 命令
- Leaves the main pane free for the orchestrator
  为主协调器保留主窗格

Use `seedPaths` when workers need access to dirty or untracked local files that are not yet part of `HEAD`, such as local orchestration scripts, draft plans, or docs:
当 worker 需要访问尚未成为 `HEAD` 一部分的脏文件或未跟踪的本地文件（如本地编排脚本、草稿计划或文档）时，使用 `seedPaths`：

```json
{
  "sessionName": "workflow-e2e",
  "seedPaths": [
    "scripts/orchestrate-worktrees.js",
    "scripts/lib/tmux-worktree-orchestrator.js",
    ".claude/plan/workflow-e2e-test.json"
  ],
  "launcherCommand": "bash {repo_root}/scripts/orchestrate-codex-worker.sh {task_file} {handoff_file} {status_file}",
  "workers": [
    { "name": "seed-check", "task": "Verify seeded files are present before starting work." }
  ]
}
```

## Troubleshooting

## Troubleshooting
## 故障排除

- **Pane not responding:** Switch to the pane directly or inspect it with `tmux capture-pane -pt <session>:0.<pane-index>`.
  **窗格无响应：** 直接切换到窗格或使用 `tmux capture-pane -pt <session>:0.<pane-index>` 检查。
- **Merge conflicts:** Use git worktrees to isolate file changes per pane.
  **合并冲突：** 使用 git worktree 隔离每个窗格的文件更改。
- **High token usage:** Reduce number of parallel panes. Each pane is a full agent session.
  **高令牌使用量：** 减少并行窗格数量。每个窗格是一个完整的代理会话。
- **tmux not found:** Install with `brew install tmux` (macOS) or `apt install tmux` (Linux).
  **找不到 tmux：** 使用 `brew install tmux`（macOS）或 `apt install tmux`（Linux）安装。
