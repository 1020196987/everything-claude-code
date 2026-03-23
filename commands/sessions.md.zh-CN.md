---
description: Manage Claude Code session history, aliases, and session metadata.
description-zh: 管理 Claude Code 会话历史、别名和会话元数据。
---

# Sessions Command
# Sessions 命令

Manage Claude Code session history - list, load, alias, and edit sessions stored in `~/.claude/sessions/`.
管理 Claude Code 会话历史 — 列出、加载、别名和编辑存储在 `~/.claude/sessions/` 中的会话。

## Usage
## 使用方式

`/sessions [list|load|alias|info|help] [options]`

## Actions
## 操作

### List Sessions
### 列出会话

Display all sessions with metadata, filtering, and pagination.
显示所有会话及元数据、过滤和分页。

Use `/sessions info` when you need operator-surface context for a swarm: branch, worktree path, and session recency.
当需要 swarm 的操作员界面上下文时使用 `/sessions info`：分支、worktree 路径和会话新旧。

```bash
/sessions                              # List all sessions (default)
/sessions                              # 列出所有会话（默认）
/sessions list                         # Same as above
/sessions list                         # 与上面相同
/sessions list --limit 10              # Show 10 sessions
/sessions list --limit 10              # 显示 10 个会话
/sessions list --date 2026-02-01       # Filter by date
/sessions list --date 2026-02-01       # 按日期过滤
/sessions list --search abc            # Search by session ID
/sessions list --search abc            # 按会话 ID 搜索
```

**Script:**
**脚本：**
```bash
node -e "
const sm = require((process.env.CLAUDE_PLUGIN_ROOT||require('path').join(require('os').homedir(),'.claude'))+'/scripts/lib/session-manager');
const aa = require((process.env.CLAUDE_PLUGIN_ROOT||require('path').join(require('os').homedir(),'.claude'))+'/scripts/lib/session-aliases');
const path = require('path');

const result = sm.getAllSessions({ limit: 20 });
const aliases = aa.listAliases();
const aliasMap = {};
for (const a of aliases) aliasMap[a.sessionPath] = a.name;

console.log('Sessions (showing ' + result.sessions.length + ' of ' + result.total + '):');
console.log('');
console.log('ID        Date        Time     Branch       Worktree           Alias');
console.log('────────────────────────────────────────────────────────────────────');

for (const s of result.sessions) {
  const alias = aliasMap[s.filename] || '';
  const metadata = sm.parseSessionMetadata(sm.getSessionContent(s.sessionPath));
  const id = s.shortId === 'no-id' ? '(none)' : s.shortId.slice(0, 8);
  const time = s.modifiedTime.toTimeString().slice(0, 5);
  const branch = (metadata.branch || '-').slice(0, 12);
  const worktree = metadata.worktree ? path.basename(metadata.worktree).slice(0, 18) : '-';

  console.log(id.padEnd(8) + ' ' + s.date + '  ' + time + '   ' + branch.padEnd(12) + ' ' + worktree.padEnd(18) + ' ' + alias);
}
"
```

### Load Session
### 加载会话

Load and display a session's content (by ID or alias).
加载并显示会话内容（按 ID 或别名）。

```bash
/sessions load <id|alias>             # Load session
/sessions load <id|alias>             # 加载会话
/sessions load 2026-02-01             # By date (for no-id sessions)
/sessions load 2026-02-01             # 按日期（对于无 ID 的会话）
/sessions load a1b2c3d4               # By short ID
/sessions load a1b2c3d4               # 按短 ID
/sessions load my-alias               # By alias name
/sessions load my-alias               # 按别名名称
```

### Create Alias
### 创建别名

Create a memorable alias for a session.
为会话创建易记的别名。

```bash
/sessions alias <id> <name>           # Create alias
/sessions alias <id> <name>           # 创建别名
/sessions alias 2026-02-01 today-work # Create alias named "today-work"
/sessions alias 2026-02-01 today-work # 创建名为 "today-work" 的别名
```

### Remove Alias
### 移除别名

Delete an existing alias.
删除现有别名。

```bash
/sessions alias --remove <name>        # Remove alias
/sessions alias --remove <name>        # 移除别名
/sessions unalias <name>               # Same as above
/sessions unalias <name>               # 与上面相同
```

### Session Info
### 会话信息

Show detailed information about a session.
显示会话的详细信息。

```bash
/sessions info <id|alias>              # Show session details
/sessions info <id|alias>              # 显示会话详情
```

### List Aliases
### 列出别名

Show all session aliases.
显示所有会话别名。

```bash
/sessions aliases                      # List all aliases
/sessions aliases                      # 列出所有别名
```

## Operator Notes
## 操作员注意事项

- Session files persist `Project`, `Branch`, and `Worktree` in the header so `/sessions info` can disambiguate parallel tmux/worktree runs.
- 会话文件在头部持久化 `Project`、`Branch` 和 `Worktree`，以便 `/sessions info` 可以区分并行的 tmux/worktree 运行。

- For command-center style monitoring, combine `/sessions info`, `git diff --stat`, and the cost metrics emitted by `scripts/hooks/cost-tracker.js`.
- 对于指挥中心风格的监控，结合 `/sessions info`、`git diff --stat` 和 `scripts/hooks/cost-tracker.js` 发出的成本指标。

## Arguments
## 参数

$ARGUMENTS:
- `list [options]` - List sessions
- `list [options]` - 列出会话

  - `--limit <n>` - Max sessions to show (default: 50)
  - `--limit <n>` - 最大显示会话数（默认：50）

  - `--date <YYYY-MM-DD>` - Filter by date
  - `--date <YYYY-MM-DD>` - 按日期过滤

  - `--search <pattern>` - Search in session ID
  - `--search <pattern>` - 在会话 ID 中搜索

- `load <id|alias>` - Load session content
- `load <id|alias>` - 加载会话内容

- `alias <id> <name>` - Create alias for session
- `alias <id> <name>` - 为会话创建别名

- `alias --remove <name>` - Remove alias
- `alias --remove <name>` - 移除别名

- `unalias <name>` - Same as `--remove`
- `unalias <name>` - 与 `--remove` 相同

- `info <id|alias>` - Show session statistics
- `info <id|alias>` - 显示会话统计

- `aliases` - List all aliases
- `aliases` - 列出所有别名

- `help` - Show this help
- `help` - 显示此帮助

## Examples
## 示例

```bash
# List all sessions
# 列出所有会话
/sessions list

# Create an alias for today's session
# 为今天的会话创建别名
/sessions alias 2026-02-01 today

# Load session by alias
# 按别名加载会话
/sessions load today

# Show session info
# 显示会话信息
/sessions info today

# Remove alias
# 移除别名
/sessions alias --remove today

# List all aliases
# 列出所有别名
/sessions aliases
```

## Notes
## 注意事项

- Sessions are stored as markdown files in `~/.claude/sessions/`
- 会话作为 markdown 文件存储在 `~/.claude/sessions/`

- Aliases are stored in `~/.claude/session-aliases.json`
- 别名存储在 `~/.claude/session-aliases.json`

- Session IDs can be shortened (first 4-8 characters usually unique enough)
- 会话 ID 可以缩短（前 4-8 个字符通常足够唯一）

- Use aliases for frequently referenced sessions
- 为经常引用的会话使用别名
