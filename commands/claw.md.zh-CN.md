---
description: Start NanoClaw v2 — ECC's persistent, zero-dependency REPL with model routing, skill hot-load, branching, compaction, export, and metrics.
description-zh: 启动 NanoClaw v2 — ECC 的持久化、零依赖 REPL，支持模型路由、技能热加载、分支、压缩、导出和指标。
---

# Claw Command
# Claw 命令

Start an interactive AI agent session with persistent markdown history and operational controls.
启动具有持久化 markdown 历史和操作控制的交互式 AI agent 会话。

## Usage
## 使用方式

```bash
node scripts/claw.js
```

Or via npm:
或者通过 npm：

```bash
npm run claw
```

## Environment Variables
## 环境变量

| Variable | Default | Description |
|----------|---------|-------------|
| `CLAW_SESSION` | `default` | Session name (alphanumeric + hyphens) |
| `CLAW_SKILLS` | *(empty)* | Comma-separated skills loaded at startup |
| `CLAW_MODEL` | `sonnet` | Default model for the session |

| 变量 | 默认值 | 描述 |
|-----|------|-----|
| `CLAW_SESSION` | `default` | 会话名称（字母数字 + 连字符） |
| `CLAW_SKILLS` | *(空)* | 启动时加载的技能，逗号分隔 |
| `CLAW_MODEL` | `sonnet` | 会话的默认模型 |

## REPL Commands
## REPL 命令

```text
/help                          Show help
/clear                         Clear current session history
/history                       Print full conversation history
/sessions                      List saved sessions
/model [name]                  Show/set model
/load <skill-name>             Hot-load a skill into context
/branch <session-name>          Branch current session
/search <query>                Search query across sessions
/compact                       Compact old turns, keep recent context
/export <md|json|txt> [path]   Export session
/metrics                       Show session metrics
exit                           Quit
```

```text
/help                          显示帮助
/clear                         清除当前会话历史
/history                       打印完整对话历史
/sessions                      列出保存的会话
/model [name]                  显示/设置模型
/load <skill-name>             热加载技能到上下文中
/branch <session-name>         分支当前会话
/search <query>                跨会话搜索查询
/compact                       压缩旧的 turns，保留最近的上下文
/export <md|json|txt> [path]   导出会话
/metrics                       显示会话指标
exit                           退出
```

## Notes
## 注意事项

- NanoClaw remains zero-dependency.
  NanoClaw 保持零依赖。
- Sessions are stored at `~/.claude/claw/<session>.md`.
  会话存储在 `~/.claude/claw/<session>.md`。
- Compaction keeps the most recent turns and writes a compaction header.
  压缩保留最近的 turns 并写入压缩头。
- Export supports markdown, JSON turns, and plain text.
  导出支持 markdown、JSON turns 和纯文本。
