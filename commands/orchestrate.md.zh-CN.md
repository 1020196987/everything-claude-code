---
description: Sequential and tmux/worktree orchestration guidance for multi-agent workflows.
description-zh: 多 agent 工作流的顺序和 tmux/worktree 编排指导。
---

# Orchestrate Command
# Orchestrate 命令

Sequential agent workflow for complex tasks.
复杂任务的顺序 agent 工作流。

## Usage
## 使用方式

`/orchestrate [workflow-type] [task-description]`

## Workflow Types
## 工作流类型

### feature
Full feature implementation workflow:
完整功能实现工作流：

```
planner -> tdd-guide -> code-reviewer -> security-reviewer
```

### bugfix
Bug investigation and fix workflow:
Bug 调查和修复工作流：

```
planner -> tdd-guide -> code-reviewer
```

### refactor
Safe refactoring workflow:
安全重构工作流：

```
architect -> code-reviewer -> tdd-guide
```

### security
Security-focused review:
安全优先审查：

```
security-reviewer -> code-reviewer -> architect
```

## Execution Pattern
## 执行模式

For each agent in the workflow:
对于工作流中的每个 agent：

1. **Invoke agent** with context from previous agent
1. **调用 agent** 并附带上一 agent 的上下文

2. **Collect output** as structured handoff document
2. **收集输出** 作为结构化交接文档

3. **Pass to next agent** in chain
3. **传递给**链中的下一 agent

4. **Aggregate results** into final report
4. **聚合结果** 到最终报告

## Handoff Document Format
## 交接文档格式

Between agents, create handoff document:
在 agent 之间，创建交接文档：

```markdown
## HANDOFF: [previous-agent] -> [next-agent]

### Context
[Summary of what was done]

### Findings
[Key discoveries or decisions]

### Files Modified
[List of files touched]

### Open Questions
[Unresolved items for next agent]

### Recommendations
[Suggested next steps]
```

## Example: Feature Workflow
## 示例：功能工作流

```
/orchestrate feature "Add user authentication"
```

Executes:
执行：

1. **Planner Agent**
1. **Planner Agent**

   - Analyzes requirements
   - 分析需求

   - Creates implementation plan
   - 创建实施计划

   - Identifies dependencies
   - 识别依赖

   - Output: `HANDOFF: planner -> tdd-guide`
   - 输出：`HANDOFF: planner -> tdd-guide`

2. **TDD Guide Agent**
2. **TDD Guide Agent**

   - Reads planner handoff
   - 读取 planner 交接

   - Writes tests first
   - 先写测试

   - Implements to pass tests
   - 实现以通过测试

   - Output: `HANDOFF: tdd-guide -> code-reviewer`
   - 输出：`HANDOFF: tdd-guide -> code-reviewer`

3. **Code Reviewer Agent**
3. **Code Reviewer Agent**

   - Reviews implementation
   - 审查实现

   - Checks for issues
   - 检查问题

   - Suggests improvements
   - 建议改进

   - Output: `HANDOFF: code-reviewer -> security-reviewer`
   - 输出：`HANDOFF: code-reviewer -> security-reviewer`

4. **Security Reviewer Agent**
4. **Security Reviewer Agent**

   - Security audit
   - 安全审计

   - Vulnerability check
   - 漏洞检查

   - Final approval
   - 最终批准

   - Output: Final Report
   - 输出：最终报告

## Final Report Format
## 最终报告格式

```
ORCHESTRATION REPORT
====================
Workflow: feature
Task: Add user authentication
Agents: planner -> tdd-guide -> code-reviewer -> security-reviewer

SUMMARY
-------
[One paragraph summary]

AGENT OUTPUTS
-------------
Planner: [summary]
TDD Guide: [summary]
Code Reviewer: [summary]
Security Reviewer: [summary]

FILES CHANGED
-------------
[List all files modified]

TEST RESULTS
------------
[Test pass/fail summary]

SECURITY STATUS
---------------
[Security findings]

RECOMMENDATION
--------------
[SHIP / NEEDS WORK / BLOCKED]
```

## Parallel Execution
## 并行执行

For independent checks, run agents in parallel:
对于独立检查，并行运行 agent：

```markdown
### Parallel Phase
Run simultaneously:
同时运行：
- code-reviewer (quality)
- code-reviewer（质量）
- security-reviewer (security)
- security-reviewer（安全）
- architect (design)
- architect（设计）

### Merge Results
Combine outputs into single report
合并输出到单一报告
```

For external tmux-pane workers with separate git worktrees, use `node scripts/orchestrate-worktrees.js plan.json --execute`. The built-in orchestration pattern stays in-process; the helper is for long-running or cross-harness sessions.
对于具有独立 git worktrees 的外部 tmux-pane workers，使用 `node scripts/orchestrate-worktrees.js plan.json --execute`。内置编排模式保持在进程内；辅助工具用于长时间运行的或跨 harness 的会话。

When workers need to see dirty or untracked local files from the main checkout, add `seedPaths` to the plan file. ECC overlays only those selected paths into each worker worktree after `git worktree add`, which keeps the branch isolated while still exposing in-flight local scripts, plans, or docs.
当 workers 需要从主 checkout 看到 dirty 或未跟踪的本地文件时，将 `seedPaths` 添加到计划文件。ECC 在 `git worktree add` 后仅将选定的那些路径覆盖到每个 worker worktree 中，这保持了分支隔离，同时仍然暴露飞行中的本地脚本、计划或文档。

```json
{
  "sessionName": "workflow-e2e",
  "seedPaths": [
    "scripts/orchestrate-worktrees.js",
    "scripts/lib/tmux-worktree-orchestrator.js",
    ".claude/plan/workflow-e2e-test.json"
  ],
  "workers": [
    { "name": "docs", "task": "Update orchestration docs." }
  ]
}
```

To export a control-plane snapshot for a live tmux/worktree session, run:
要为活动的 tmux/worktree 会话导出控制平面快照，运行：

```bash
node scripts/orchestration-status.js .claude/plan/workflow-visual-proof.json
```

The snapshot includes session activity, tmux pane metadata, worker states, objectives, seeded overlays, and recent handoff summaries in JSON form.
快照包括会话活动、tmux pane 元数据、worker 状态、目标、播种的覆盖和最近交接摘要（JSON 形式）。

## Operator Command-Center Handoff
## 操作员指挥中心交接

When the workflow spans multiple sessions, worktrees, or tmux panes, append a control-plane block to the final handoff:
当工作流跨多个会话、worktrees 或 tmux panes 时，将控制平面块追加到最终交接：

```markdown
CONTROL PLANE
-------------
Sessions:
- active session ID or alias
- 每个活动 worker 的分支 + worktree 路径
- tmux pane or detached session name when applicable

Diffs:
- git status summary
- git diff --stat for touched files
- merge/conflict risk notes

Approvals:
- pending user approvals
- blocked steps awaiting confirmation

Telemetry:
- last activity timestamp or idle signal
- estimated token or cost drift
- policy events raised by hooks or reviewers
```

This keeps planner, implementer, reviewer, and loop workers legible from the operator surface.
这使 planner、实现者、审查者和循环 workers 在操作员界面上清晰可见。

## Arguments
## 参数

$ARGUMENTS:
- `feature <description>` - Full feature workflow
- `feature <description>` - 完整功能工作流

- `bugfix <description>` - Bug fix workflow
- `bugfix <description>` - Bug 修复工作流

- `refactor <description>` - Refactoring workflow
- `refactor <description>` - 重构工作流

- `security <description>` - Security review workflow
- `security <description>` - 安全审查工作流

- `custom <agents> <description>` - Custom agent sequence
- `custom <agents> <description>` - 自定义 agent 序列

## Custom Workflow Example
## 自定义工作流示例

```
/orchestrate custom "architect,tdd-guide,code-reviewer" "Redesign caching layer"
```

## Tips
## 技巧

1. **Start with planner** for complex features
1. **对于复杂功能从 planner 开始**

2. **Always include code-reviewer** before merge
2. **合并前始终包含 code-reviewer**

3. **Use security-reviewer** for auth/payment/PII
3. **对于 auth/支付/PII 使用 security-reviewer**

4. **Keep handoffs concise** - focus on what next agent needs
4. **保持交接简洁** - 关注下一 agent 需要什么

5. **Run verification** between agents if needed
5. **如需要，在 agent 之间运行验证**
