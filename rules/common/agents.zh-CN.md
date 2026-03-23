---
name: Agents
description: Agent orchestration and task allocation rules
description: Agent 编排和任务分配规则
---

# Agent Orchestration
# Agent 编排

## Available Agents
## 可用 Agent

Located in `~/.claude/agents/`:
位于 `~/.claude/agents/`：

| Agent | Purpose | When to Use |
|-------|---------|-------------|
| planner | Implementation planning | Complex features, refactoring |
| architect | System design | Architectural decisions |
| tdd-guide | Test-driven development | New features, bug fixes |
| code-reviewer | Code review | After writing code |
| security-reviewer | Security analysis | Before commits |
| build-error-resolver | Fix build errors | When build fails |
| e2e-runner | E2E testing | Critical user flows |
| refactor-cleaner | Dead code cleanup | Code maintenance |
| doc-updater | Documentation | Updating docs |
| rust-reviewer | Rust code review | Rust projects |

## Immediate Agent Usage
## 立即使用 Agent

No user prompt needed:
无需用户提示：

1. Complex feature requests - Use **planner** agent
   复杂功能请求 - 使用 **planner** agent
2. Code just written/modified - Use **code-reviewer** agent
   刚编写/修改的代码 - 使用 **code-reviewer** agent
3. Bug fix or new feature - Use **tdd-guide** agent
   Bug 修复或新功能 - 使用 **tdd-guide** agent
4. Architectural decision - Use **architect** agent
   架构决策 - 使用 **architect** agent

## Parallel Task Execution
## 并行任务执行

ALWAYS use parallel Task execution for independent operations:
对于独立操作，始终使用并行 Task 执行：

```markdown
# GOOD: Parallel execution
# 好：并行执行
Launch 3 agents in parallel:
并行启动 3 个 agent：
1. Agent 1: Security analysis of auth module
   Agent 1：认证模块安全分析
2. Agent 2: Performance review of cache system
   Agent 2：缓存系统性能审查
3. Agent 3: Type checking of utilities
   Agent 3：工具类型检查

# BAD: Sequential when unnecessary
# 不好：不必要时顺序执行
First agent 1, then agent 2, then agent 3
先运行 agent 1，然后 agent 2，然后 agent 3
```

## Multi-Perspective Analysis
## 多视角分析

For complex problems, use split role sub-agents:
对于复杂问题，使用分裂角色的子 agent：

- Factual reviewer
  事实审查员
- Senior engineer
  高级工程师
- Security expert
  安全专家
- Consistency reviewer
  一致性审查员
- Redundancy checker
  冗余检查器
