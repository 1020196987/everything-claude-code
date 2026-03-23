---
name: claude-devfleet
description: Orchestrate multi-agent coding tasks via Claude DevFleet — plan projects, dispatch parallel agents in isolated worktrees, monitor progress, and read structured reports.
description zh-CN: 通过 Claude DevFleet 编排多智能体编码任务——规划项目、在隔离的 worktree 中派遣并行智能体、监控进度并读取结构化报告。
origin: community
---

# Claude DevFleet Multi-Agent Orchestration
# Claude DevFleet 多智能体编排

## When to Use
## 何时使用

Use this skill when you need to dispatch multiple Claude Code agents to work on coding tasks in parallel. Each agent runs in an isolated git worktree with full tooling.
当您需要派遣多个 Claude Code 智能体并行处理编码任务时使用此技能。每个智能体在隔离的 git worktree 中运行，拥有完整工具。

Requires a running Claude DevFleet instance connected via MCP:
需要通过 MCP 连接正在运行的 Claude DevFleet 实例：
```bash
claude mcp add devfleet --transport http http://localhost:18801/mcp
```

## How It Works
## 工作原理

```
User → "Build a REST API with auth and tests"
用户 → "构建带认证和测试的 REST API"
  ↓
plan_project(prompt) → project_id + mission DAG
  ↓
Show plan to user → get approval
显示计划给用户 → 获得批准
  ↓
dispatch_mission(M1) → Agent 1 spawns in worktree
  ↓
M1 completes → auto-merge → auto-dispatch M2 (depends_on M1)
M1 完成 → 自动合并 → 自动派遣 M2（取决于 M1）
  ↓
M2 completes → auto-merge
M2 完成 → 自动合并
  ↓
get_report(M2) → files_changed, what_done, errors, next_steps
  ↓
Report back to user
向用户报告
```

### Tools
### 工具

| Tool | Purpose |
| 工具 | 用途 |
|------|---------|
| `plan_project(prompt)` | AI breaks a description into a project with chained missions |
| `plan_project(prompt)` | AI 将描述分解为包含链式任务的项目 |
| `create_project(name, path?, description?)` | Create a project manually, returns `project_id` |
| `create_project(name, path?, description?)` | 手动创建项目，返回 `project_id` |
| `create_mission(project_id, title, prompt, depends_on?, auto_dispatch?)` | Add a mission. `depends_on` is a list of mission ID strings (e.g., `["abc-123"]`). Set `auto_dispatch=true` to auto-start when deps are met. |
| `create_mission(project_id, title, prompt, depends_on?, auto_dispatch?)` | 添加任务。`depends_on` 是任务 ID 字符串列表（例如 `["abc-123"]`）。设置 `auto_dispatch=true` 在依赖满足时自动启动。 |
| `dispatch_mission(mission_id, model?, max_turns?)` | Start an agent on a mission |
| `dispatch_mission(mission_id, model?, max_turns?)` | 在任务上启动智能体 |
| `cancel_mission(mission_id)` | Stop a running agent |
| `cancel_mission(mission_id)` | 停止运行中的智能体 |
| `wait_for_mission(mission_id, timeout_seconds?)` | Block until a mission completes (see note below) |
| `wait_for_mission(mission_id, timeout_seconds?)` | 阻塞直到任务完成（见下方说明） |
| `get_mission_status(mission_id)` | Check mission progress without blocking |
| `get_mission_status(mission_id)` | 非阻塞检查任务进度 |
| `get_report(mission_id)` | Read structured report (files changed, tested, errors, next steps) |
| `get_report(mission_id)` | 读取结构化报告（变更文件、完成内容、错误、下一步） |
| `get_dashboard()` | System overview: running agents, stats, recent activity |
| `get_dashboard()` | 系统概览：运行中的智能体、统计、最近活动 |
| `list_projects()` | Browse all projects |
| `list_projects()` | 浏览所有项目 |
| `list_missions(project_id, status?)` | List missions in a project |
| `list_missions(project_id, status?)` | 列出项目中的任务 |

> **Note on `wait_for_mission`:** This blocks the conversation for up to `timeout_seconds` (default 600). For long-running missions, prefer polling with `get_mission_status` every 30–60 seconds instead, so the user sees progress updates.
> **关于 `wait_for_mission` 的说明：** 这会阻塞对话最多 `timeout_seconds`（默认600秒）。对于长时间运行的任务，优先每30-60秒用 `get_mission_status` 轮询，这样用户可以看到进度更新。

### Workflow: Plan → Dispatch → Monitor → Report
### 工作流：规划 → 派遣 → 监控 → 报告

1. **Plan**: Call `plan_project(prompt="...")` → returns `project_id` + list of missions with `depends_on` chains and `auto_dispatch=true`.
   - **规划**：调用 `plan_project(prompt="...")` → 返回 `project_id` + 带 `depends_on` 链和 `auto_dispatch=true` 的任务列表。
2. **Show plan**: Present mission titles, types, and dependency chain to the user.
   - **显示计划**：向用户展示任务标题、类型和依赖链。
3. **Dispatch**: Call `dispatch_mission(mission_id=<first_mission_id>)` on the root mission (empty `depends_on`). Remaining missions auto-dispatch as their dependencies complete (because `plan_project` sets `auto_dispatch=true` on them).
   - **派遣**：在根任务（空的 `depends_on`）上调用 `dispatch_mission(mission_id=<first_mission_id>)`。其余任务在依赖完成时自动派遣（因为 `plan_project` 在它们上设置了 `auto_dispatch=true`）。
4. **Monitor**: Call `get_mission_status(mission_id=...)` or `get_dashboard()` to check progress.
   - **监控**：调用 `get_mission_status(mission_id=...)` 或 `get_dashboard()` 检查进度。
5. **Report**: Call `get_report(mission_id=...)` when missions complete. Share highlights with the user.
   - **报告**：任务完成时调用 `get_report(mission_id=...)`。与用户分享亮点。

### Concurrency
### 并发性

DevFleet runs up to 3 concurrent agents by default (configurable via `DEVFLEET_MAX_AGENTS`). When all slots are full, missions with `auto_dispatch=true` queue in the mission watcher and dispatch automatically as slots free up. Check `get_dashboard()` for current slot usage.
DevFleet 默认最多运行3个并发智能体（可通过 `DEVFLEET_MAX_AGENTS` 配置）。当所有槽位满时，带 `auto_dispatch=true` 的任务在任务监视器中排队，槽位空闲时自动派遣。检查 `get_dashboard()` 获取当前槽位使用情况。

## Examples
## 示例

### Full auto: plan and launch
### 全自动：规划和启动

1. `plan_project(prompt="...")` → shows plan with missions and dependencies.
   - `plan_project(prompt="...")` → 显示带任务和依赖的计划。
2. Dispatch the first mission (the one with empty `depends_on`).
   - 派遣第一个任务（空 `depends_on` 的那个）。
3. Remaining missions auto-dispatch as dependencies resolve (they have `auto_dispatch=true`).
   - 剩余任务在依赖解决时自动派遣（它们有 `auto_dispatch=true`）。
4. Report back with project ID and mission count so the user knows what was launched.
   - 返回报告含项目 ID 和任务数量，让用户知道启动了哪些。
5. Poll with `get_mission_status` or `get_dashboard()` periodically until all missions reach a terminal state (`completed`, `failed`, or `cancelled`).
   - 定期用 `get_mission_status` 或 `get_dashboard()` 轮询，直到所有任务达到终态（`completed`、`failed` 或 `cancelled`）。
6. `get_report(mission_id=...)` for each terminal mission — summarize successes and call out failures with errors and next steps.
   - 对每个终态任务调用 `get_report(mission_id=...)` —— 总结成功并用错误和下一步指出失败。

### Manual: step-by-step control
### 手动：逐步控制

1. `create_project(name="My Project")` → returns `project_id`.
   - `create_project(name="My Project")` → 返回 `project_id`。
2. `create_mission(project_id=project_id, title="...", prompt="...", auto_dispatch=true)` for the first (root) mission → capture `root_mission_id`.
   - `create_mission(project_id=project_id, title="...", prompt="...", auto_dispatch=true)` 首个（根）任务 → 捕获 `root_mission_id`。
   `create_mission(project_id=project_id, title="...", prompt="...", auto_dispatch=true, depends_on=["<root_mission_id>"])` for each subsequent task.
   - 每个后续任务的 `create_mission(project_id=project_id, title="...", prompt="...", auto_dispatch=true, depends_on=["<root_mission_id>"])`。
3. `dispatch_mission(mission_id=...)` on the first mission to start the chain.
   - 在第一个任务上调用 `dispatch_mission(mission_id=...)` 启动链。
4. `get_report(mission_id=...)` when done.
   - 完成后 `get_report(mission_id=...)`。

### Sequential with review
### 带审查的顺序执行

1. `create_project(name="...")` → get `project_id`.
   - `create_project(name="...")` → 获取 `project_id`。
2. `create_mission(project_id=project_id, title="Implement feature", prompt="...")` → get `impl_mission_id`.
   - `create_mission(project_id=project_id, title="Implement feature", prompt="...")` → 获取 `impl_mission_id`。
3. `dispatch_mission(mission_id=impl_mission_id)`, then poll with `get_mission_status` until complete.
   - `dispatch_mission(mission_id=impl_mission_id)`，然后用 `get_mission_status` 轮询直到完成。
4. `get_report(mission_id=impl_mission_id)` to review results.
   - `get_report(mission_id=impl_mission_id)` 审查结果。
5. `create_mission(project_id=project_id, title="Review", prompt="...", depends_on=[impl_mission_id], auto_dispatch=true)` — auto-starts since the dependency is already met.
   - `create_mission(project_id=project_id, title="Review", prompt="...", depends_on=[impl_mission_id], auto_dispatch=true)` —— 因为依赖已满足，自动启动。

## Guidelines
## 指南

- Always confirm the plan with the user before dispatching, unless they said to go ahead.
  - 在派遣前始终向用户确认计划，除非他们说继续。
- Include mission titles and IDs when reporting status.
  - 报告状态时包含任务标题和 ID。
- If a mission fails, read its report before retrying.
  - 如果任务失败，在重试前阅读其报告。
- Check `get_dashboard()` for agent slot availability before bulk dispatching.
  - 批量派遣前检查 `get_dashboard()` 获取智能体槽位可用性。
- Mission dependencies form a DAG — do not create circular dependencies.
  - 任务依赖形成 DAG —— 不要创建循环依赖。
- Each agent runs in an isolated git worktree and auto-merges on completion. If a merge conflict occurs, the changes remain on the agent's worktree branch for manual resolution.
  - 每个智能体在隔离的 git worktree 中运行，完成时自动合并。如果发生合并冲突，变更保留在智能体的 worktree 分支上以便手动解决。
- When manually creating missions, always set `auto_dispatch=true` if you want them to trigger automatically when dependencies complete. Without this flag, missions stay in `draft` status.
  - 手动创建任务时，如果您希望它们在依赖完成时自动触发，始终设置 `auto_dispatch=true`。没有此标志，任务保持 `draft` 状态。
