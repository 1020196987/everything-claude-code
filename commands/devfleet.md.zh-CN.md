---
description: Orchestrate parallel Claude Code agents via Claude DevFleet — plan projects from natural language, dispatch agents in isolated worktrees, monitor progress, and read structured reports.
description-zh: 通过 Claude DevFleet 编排并行 Claude Code agents — 从自然语言规划项目，在隔离的 worktrees 中分发 agents，监控进度，并读取结构化报告。
---

# DevFleet — Multi-Agent Orchestration
# DevFleet — 多 Agent 编排

Orchestrate parallel Claude Code agents via Claude DevFleet. Each agent runs in an isolated git worktree with full tooling.
通过 Claude DevFleet 编排并行 Claude Code agents。每个 agent 在具有完整工具的独立 git worktree 中运行。

Requires the DevFleet MCP server: `claude mcp add devfleet --transport http http://localhost:18801/mcp`
需要 DevFleet MCP 服务器：`claude mcp add devfleet --transport http http://localhost:18801/mcp`

## Flow
## 流程

```
User describes project
  → plan_project(prompt) → mission DAG with dependencies
  → Show plan, get approval
  → dispatch_mission(M1) → Agent spawns in worktree
  → M1 completes → auto-merge → M2 auto-dispatches (depends_on M1)
  → M2 completes → auto-merge
  → get_report(M2) → files_changed, what_done, errors, next_steps
  → Report summary to user
```

## Workflow
## 工作流

1. **Plan the project** from the user's description:
1. **从用户描述规划项目**：

```
mcp__devfleet__plan_project(prompt="<user's description>")
```

This returns a project with chained missions. Show the user:
这返回一个带有链式 missions 的项目。向用户显示：

- Project name and ID
- 项目名称和 ID

- Each mission: title, type, dependencies
- 每个 mission：标题、类型、依赖

- The dependency DAG (which missions block which)
- 依赖 DAG（哪些 missions 阻止哪些）

2. **Wait for user approval** before dispatching. Show the plan clearly.
2. **在分发前等待用户批准**。清楚地显示计划。

3. **Dispatch the first mission** (the one with empty `depends_on`):
3. **分发第一个 mission**（空 `depends_on` 的那个）：

```
mcp__devfleet__dispatch_mission(mission_id="<first_mission_id>")
```

The remaining missions auto-dispatch as their dependencies complete (because `plan_project` creates them with `auto_dispatch=true`). When manually creating missions with `create_mission`, you must explicitly set `auto_dispatch=true` for this behavior.
剩余的 missions 在其依赖完成时自动分发（因为 `plan_project` 用 `auto_dispatch=true` 创建它们）。当手动用 `create_mission` 创建 missions 时，必须明确设置 `auto_dispatch=true` 以实现此行为。

4. **Monitor progress** — check what's running:
4. **监控进度** — 检查正在运行的内容：

```
mcp__devfleet__get_dashboard()
```

Or check a specific mission:
或检查特定 mission：

```
mcp__devfleet__get_mission_status(mission_id="<id>")
```

Prefer polling with `get_mission_status` over `wait_for_mission` for long-running missions, so the user sees progress updates.
对于长时间运行的 missions，优先使用 `get_mission_status` 轮询而不是 `wait_for_mission`，以便用户看到进度更新。

5. **Read the report** for each completed mission:
5. **读取每个完成 mission 的报告**：

```
mcp__devfleet__get_report(mission_id="<mission_id>")
```

Call this for every mission that reached a terminal state. Reports contain: files_changed, what_done, what_open, what_tested, what_untested, next_steps, errors_encountered.
对每个达到终端状态的 mission 调用此命令。报告包含：files_changed、what_done、what_open、what_tested、what_untested、next_steps、errors_encountered。

## All Available Tools
## 所有可用工具

| Tool | Purpose |
|------|---------|
| `plan_project(prompt)` | AI breaks description into chained missions with `auto_dispatch=true` |
| `plan_project(prompt)` | AI 将描述分解为带 `auto_dispatch=true` 的链式 missions |
| `create_project(name, path?, description?)` | Create a project manually, returns `project_id` |
| `create_project(name, path?, description?)` | 手动创建项目，返回 `project_id` |
| `create_mission(project_id, title, prompt, depends_on?, auto_dispatch?)` | Add a mission. `depends_on` is a list of mission ID strings. |
| `create_mission(project_id, title, prompt, depends_on?, auto_dispatch?)` | 添加 mission。`depends_on` 是 mission ID 字符串列表。 |
| `dispatch_mission(mission_id, model?, max_turns?)` | Start an agent |
| `dispatch_mission(mission_id, model?, max_turns?)` | 启动 agent |
| `cancel_mission(mission_id)` | Stop a running agent |
| `cancel_mission(mission_id)` | 停止正在运行的 agent |
| `wait_for_mission(mission_id, timeout_seconds?)` | Block until done (prefer polling for long tasks) |
| `wait_for_mission(mission_id, timeout_seconds?)` | 阻塞直到完成（对长任务优先轮询） |
| `get_mission_status(mission_id)` | Check progress without blocking |
| `get_mission_status(mission_id)` | 不阻塞地检查进度 |
| `get_report(mission_id)` | Read structured report |
| `get_report(mission_id)` | 读取结构化报告 |
| `get_dashboard()` | System overview |
| `get_dashboard()` | 系统概览 |
| `list_projects()` | Browse projects |
| `list_projects()` | 浏览项目 |
| `list_missions(project_id, status?)` | List missions |
| `list_missions(project_id, status?)` | 列出 missions |

## Guidelines
## 指南

- Always confirm the plan before dispatching unless the user said "go ahead"
- 除非用户说"继续"，否则始终在分发前确认计划

- Include mission titles and IDs when reporting status
- 报告状态时包含 mission 标题和 ID

- If a mission fails, read its report to understand errors before retrying
- 如果 mission 失败，在重试前读取其报告以了解错误

- Agent concurrency is configurable (default: 3). Excess missions queue and auto-dispatch as slots free up. Check `get_dashboard()` for slot availability.
- Agent 并发可配置（默认：3）。超额 missions 排队并在插槽空闲时自动分发。检查 `get_dashboard()` 的插槽可用性。

- Dependencies form a DAG — never create circular dependencies
- 依赖形成 DAG — 永远不要创建循环依赖

- Each agent auto-merges its worktree on completion. If a merge conflict occurs, the changes remain on the worktree branch for manual resolution.
- 每个 agent 在完成时自动合并其 worktree。如果发生合并冲突，更改保留在 worktree 分支上以便手动解决。
