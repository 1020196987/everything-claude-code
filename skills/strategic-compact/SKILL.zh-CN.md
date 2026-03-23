---
name: strategic-compact
description: Suggests manual context compaction at logical intervals to preserve context through task phases rather than arbitrary auto-compaction.
description zh-CN: 建议在逻辑间隔进行手动上下文压缩，以在任务阶段之间保留上下文，而不是任意的自动压缩。
origin: ECC
---

# Strategic Compact Skill
# 战略压缩技能

Suggests manual `/compact` at strategic points in your workflow rather than relying on arbitrary auto-compaction.
建议在您工作流程中的战略时机进行手动的 `/compact`，而不是依赖任意的自动压缩。

## When to Activate
## 何时激活

- Running long sessions that approach context limits (200K+ tokens)
- 运行接近上下文限制的长会话（200K+ tokens）
- Working on multi-phase tasks (research → plan → implement → test)
- 处理多阶段任务（研究 → 计划 → 实现 → 测试）
- Switching between unrelated tasks within the same session
- 在同一会话中切换不相关的任务
- After completing a major milestone and starting new work
- 完成一个主要里程碑后开始新工作
- When responses slow down or become less coherent (context pressure)
- 当响应变慢或变得不那么连贯时（上下文压力）

## Why Strategic Compaction?
## 为什么需要战略压缩？

Auto-compaction triggers at arbitrary points:
自动压缩在任意时间点触发：

- Often mid-task, losing important context
- 经常在任务中途触发，丢失重要上下文
- No awareness of logical task boundaries
- 不了解逻辑任务边界
- Can interrupt complex multi-step operations
- 可能中断复杂的多步骤操作

Strategic compaction at logical boundaries:
在逻辑边界进行战略压缩：

- **After exploration, before execution** — Compact research context, keep implementation plan
- **探索后，执行前** — 压缩研究上下文，保留实现计划
- **After completing a milestone** — Fresh start for next phase
- **完成里程碑后** — 为下一阶段全新开始
- **Before major context shifts** — Clear exploration context before different task
- **重大上下文切换前** — 在不同任务前清除探索上下文

## How It Works
## 工作原理

The `suggest-compact.js` script runs on PreToolUse (Edit/Write) and:
`suggest-compact.js` 脚本在 PreToolUse（Edit/Write）上运行，并：

1. **Tracks tool calls** — Counts tool invocations in session
1. **跟踪工具调用** — 计算会话中的工具调用次数
2. **Threshold detection** — Suggests at configurable threshold (default: 50 calls)
2. **阈值检测** — 在可配置阈值（默认：50 次调用）时建议
3. **Periodic reminders** — Reminds every 25 calls after threshold
3. **定期提醒** — 阈值后每 25 次调用提醒一次

## Hook Setup
## Hook 设置

Add to your `~/.claude/settings.json`:
添加到您的 `~/.claude/settings.json`：

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit",
        "hooks": [{ "type": "command", "command": "node ~/.claude/skills/strategic-compact/suggest-compact.js" }]
      },
      {
        "matcher": "Write",
        "hooks": [{ "type": "command", "command": "node ~/.claude/skills/strategic-compact/suggest-compact.js" }]
      }
    ]
  }
}
```

## Configuration
## 配置

Environment variables:
环境变量：

- `COMPACT_THRESHOLD` — Tool calls before first suggestion (default: 50)
- `COMPACT_THRESHOLD` — 首次建议前的工具调用次数（默认：50）

## Compaction Decision Guide
## 压缩决策指南

Use this table to decide when to compact:
使用此表决定何时压缩：

| Phase Transition | Compact? | Why |
|-----------------|----------|-----|
| Research → Planning | Yes | Research context is bulky; plan is the distilled output |
| Planning → Implementation | Yes | Plan is in TodoWrite or a file; free up context for code |
| Implementation → Testing | Maybe | Keep if tests reference recent code; compact if switching focus |
| Debugging → Next feature | Yes | Debug traces pollute context for unrelated work |
| Mid-implementation | No | Losing variable names, file paths, and partial state is costly |
| After a failed approach | Yes | Clear the dead-end reasoning before trying a new approach |

| 阶段转换 | 压缩？ | 为什么 |
|-----------------|----------|-----|
| 研究 → 计划 | 是 | 研究上下文庞大；计划是提炼后的输出 |
| 计划 → 实现 | 是 | 计划在 TodoWrite 或文件中；释放上下文用于代码 |
| 实现 → 测试 | 可能 | 如果测试引用最近代码则保留；如果切换焦点则压缩 |
| 调试 → 下一个功能 | 是 | 调试痕迹污染了不相关工作的上下文 |
| 实现中途 | 否 | 丢失变量名、文件路径和部分状态代价高昂 |
| 失败的方法后 | 是 | 在尝试新方法前清除死胡同推理 |

## What Survives Compaction
## 压缩后保留什么

Understanding what persists helps you compact with confidence:
了解什么会保留帮助您有信心地进行压缩：

| Persists | Lost |
|----------|------|
| CLAUDE.md instructions | Intermediate reasoning and analysis |
| TodoWrite task list | File contents you previously read |
| Memory files (`~/.claude/memory/`) | Multi-step conversation context |
| Git state (commits, branches) | Tool call history and counts |
| Files on disk | Nuanced user preferences stated verbally |

| 保留 | 丢失 |
|----------|------|
| CLAUDE.md 指令 | 中间推理和分析 |
| TodoWrite 任务列表 | 您之前读取的文件内容 |
| 内存文件（`~/.claude/memory/`） | 多步骤对话上下文 |
| Git 状态（提交、分支） | 工具调用历史和计数 |
| 磁盘上的文件 | 口头表达的用户微妙偏好 |

## Best Practices
## 最佳实践

1. **Compact after planning** — Once plan is finalized in TodoWrite, compact to start fresh
1. **计划后压缩** — 一旦计划在 TodoWrite 中最终确定，压缩以重新开始
2. **Compact after debugging** — Clear error-resolution context before continuing
2. **调试后压缩** — 继续前清除错误解决上下文
3. **Don't compact mid-implementation** — Preserve context for related changes
3. **实现中途不要压缩** — 为相关更改保留上下文
4. **Read the suggestion** — The hook tells you *when*, you decide *if*
4. **阅读建议** — hook 告诉您*何时*，您决定*是否*
5. **Write before compacting** — Save important context to files or memory before compacting
5. **压缩前写入** — 压缩前将重要上下文保存到文件或内存
6. **Use `/compact` with a summary** — Add a custom message: `/compact Focus on implementing auth middleware next`
6. **使用带摘要的 `/compact`** — 添加自定义消息：`/compact Focus on implementing auth middleware next`

## Token Optimization Patterns
## Token 优化模式

### Trigger-Table Lazy Loading
### 触发表延迟加载

Instead of loading full skill content at session start, use a trigger table that maps keywords to skill paths. Skills load only when triggered, reducing baseline context by 50%+:
不要在会话开始时加载完整的 skill 内容，而是使用将关键字映射到 skill 路径的触发表。Skills 仅在触发时加载，将基线上下文减少 50%+：

| Trigger | Skill | Load When |
|---------|-------|-----------|
| "test", "tdd", "coverage" | tdd-workflow | User mentions testing |
| "security", "auth", "xss" | security-review | Security-related work |
| "deploy", "ci/cd" | deployment-patterns | Deployment context |

| 触发器 | Skill | 何时加载 |
|---------|-------|-----------|
| "test", "tdd", "coverage" | tdd-workflow | 用户提到测试时 |
| "security", "auth", "xss" | security-review | 安全相关工作时 |
| "deploy", "ci/cd" | deployment-patterns | 部署上下文 |

### Context Composition Awareness
### 上下文组成意识

Monitor what's consuming your context window:
监控什么在消耗您的上下文窗口：

- **CLAUDE.md files** — Always loaded, keep lean
- **CLAUDE.md 文件** — 始终加载，保持精简
- **Loaded skills** — Each skill adds 1-5K tokens
- **已加载的 skills** — 每个 skill 增加 1-5K tokens
- **Conversation history** — Grows with each exchange
- **对话历史** — 随每次交换增长
- **Tool results** — File reads, search results add bulk
- **工具结果** — 文件读取、搜索结果增加大量

### Duplicate Instruction Detection
### 重复指令检测

Common sources of duplicate context:
重复上下文的常见来源：

- Same rules in both `~/.claude/rules/` and project `.claude/rules/`
- `~/.claude/rules/` 和项目 `.claude/rules/` 中的相同规则
- Skills that repeat CLAUDE.md instructions
- 重复 CLAUDE.md 指令的 skills
- Multiple skills covering overlapping domains
- 覆盖重叠领域的多个 skills

### Context Optimization Tools
### 上下文优化工具

- `token-optimizer` MCP — Automated 95%+ token reduction via content deduplication
- `token-optimizer` MCP — 通过内容去重自动减少 95%+ tokens
- `context-mode` — Context virtualization (315KB to 5.4KB demonstrated)
- `context-mode` — 上下文虚拟化（已演示从 315KB 到 5.4KB）

## Related
## 相关

- [The Longform Guide](https://x.com/affaanmustafa/status/2014040193557471352) — Token optimization section
- [The Longform Guide](https://x.com/affaanmustafa/status/2014040193557471352) — Token 优化部分
- Memory persistence hooks — For state that survives compaction
- 内存持久化 hooks — 用于在压缩后保留状态
- `continuous-learning` skill — Extracts patterns before session ends
- `continuous-learning` skill — 在会话结束前提取模式
