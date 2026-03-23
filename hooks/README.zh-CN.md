---
name: hooks
description: Event-driven automations that fire before or after Claude Code tool executions
description: 在 Claude Code 工具执行之前或之后触发的事件驱动自动化
---

# Hooks
# Hooks

Hooks are event-driven automations that fire before or after Claude Code tool executions. They enforce code quality, catch mistakes early, and automate repetitive checks.
Hooks 是事件驱动的自动化，在 Claude Code 工具执行之前或之后触发。它们强制执行代码质量，及早发现错误，并自动执行重复检查。

## How Hooks Work
## 工作原理

```
User request → Claude picks a tool → PreToolUse hook runs → Tool executes → PostToolUse hook runs
用户请求 → Claude 选择工具 → PreToolUse hook 运行 → 工具执行 → PostToolUse hook 运行
```

- **PreToolUse** hooks run before the tool executes. They can **block** (exit code 2) or **warn** (stderr without blocking).
  **PreToolUse** hooks 在工具执行之前运行。它们可以**阻止**（退出码 2）或**警告**（stderr 而不阻止）。

- **PostToolUse** hooks run after the tool completes. They can analyze output but cannot block.
  **PostToolUse** hooks 在工具完成后运行。它们可以分析输出但不能阻止。

- **Stop** hooks run after each Claude response.
  **Stop** hooks 在每次 Claude 响应后运行。

- **SessionStart/SessionEnd** hooks run at session lifecycle boundaries.
  **SessionStart/SessionEnd** hooks 在会话生命周期边界运行。

- **PreCompact** hooks run before context compaction, useful for saving state.
  **PreCompact** hooks 在上下文压缩之前运行，可用于保存状态。

## Hooks in This Plugin
## 本插件中的 Hooks

### PreToolUse Hooks
### PreToolUse Hooks

| Hook | Matcher | Behavior | Exit Code |
|------|---------|----------|-----------|
| Hook | 匹配器 | 行为 | 退出码 |
| **Dev server blocker** | `Bash` | Blocks `npm run dev` etc. outside tmux — ensures log access | 2 (blocks) |
| **开发服务器阻止器** | `Bash` | 在 tmux 外阻止 `npm run dev` 等 — 确保日志访问 | 2 (阻止) |
| **Tmux reminder** | `Bash` | Suggests tmux for long-running commands (npm test, cargo build, docker) | 0 (warns) |
| **Tmux 提醒** | `Bash` | 为长时间运行的命令建议使用 tmux (npm test, cargo build, docker) | 0 (警告) |
| **Git push reminder** | `Bash` | Reminds to review changes before `git push` | 0 (warns) |
| **Git push 提醒** | `Bash` | 在 `git push` 前提醒检查变更 | 0 (警告) |
| **Doc file warning** | `Write` | Warns about non-standard `.md`/`.txt` files (allows README, CLAUDE, CONTRIBUTING, CHANGELOG, LICENSE, SKILL, docs/, skills/); cross-platform path handling | 0 (warns) |
| **文档文件警告** | `Write` | 警告非标准的 `.md`/`.txt` 文件（允许 README, CLAUDE, CONTRIBUTING, CHANGELOG, LICENSE, SKILL, docs/, skills/）；跨平台路径处理 | 0 (警告) |
| **Strategic compact** | `Edit\|Write` | Suggests manual `/compact` at logical intervals (every ~50 tool calls) | 0 (warns) |
| **策略性压缩** | `Edit\|Write` | 在逻辑间隔建议手动 `/compact`（每约 50 次工具调用） | 0 (警告) |
| **InsAIts security monitor (opt-in)** | `Bash\|Write\|Edit\|MultiEdit` | Optional security scan for high-signal tool inputs. Disabled unless `ECC_ENABLE_INSAITS=1`. Blocks on critical findings, warns on non-critical, and writes audit log to `.insaits_audit_session.jsonl`. Requires `pip install insa-its`. [Details](../scripts/hooks/insaits-security-monitor.py) | 2 (blocks critical) / 0 (warns) |
| **InsAIts 安全监控（可选）** | `Bash\|Write\|Edit\|MultiEdit` | 可选的高信号工具输入安全扫描。除非设置 `ECC_ENABLE_INSAITS=1`，否则禁用。阻止严重发现，警告非严重发现，并将审计日志写入 `.insaits_audit_session.jsonl`。需要 `pip install insa-its`。[详情](../scripts/hooks/insaits-security-monitor.py) | 2 (阻止严重) / 0 (警告) |

### PostToolUse Hooks
### PostToolUse Hooks

| Hook | Matcher | What It Does |
|------|---------|-------------|
| Hook | 匹配器 | 功能 |
| **PR logger** | `Bash` | Logs PR URL and review command after `gh pr create` |
| **PR 日志** | `Bash` | 在 `gh pr create` 后记录 PR URL 和审查命令 |
| **Build analysis** | `Bash` | Background analysis after build commands (async, non-blocking) |
| **构建分析** | `Bash` | 构建命令后的后台分析（异步，非阻塞） |
| **Quality gate** | `Edit\|Write\|MultiEdit` | Runs fast quality checks after edits |
| **质量门禁** | `Edit\|Write\|MultiEdit` | 编辑后运行快速质量检查 |
| **Prettier format** | `Edit` | Auto-formats JS/TS files with Prettier after edits |
| **Prettier 格式化** | `Edit` | 编辑后用 Prettier 自动格式化 JS/TS 文件 |
| **TypeScript check** | `Edit` | Runs `tsc --noEmit` after editing `.ts`/`.tsx` files |
| **TypeScript 检查** | `Edit` | 编辑 `.ts`/`.tsx` 文件后运行 `tsc --noEmit` |
| **console.log warning** | `Edit` | Warns about `console.log` statements in edited files |
| **console.log 警告** | `Edit` | 警告编辑文件中的 `console.log` 语句 |

### Lifecycle Hooks
### 生命周期 Hooks

| Hook | Event | What It Does |
|------|-------|-------------|
| Hook | 事件 | 功能 |
| **Session start** | `SessionStart` | Loads previous context and detects package manager |
| **会话开始** | `SessionStart` | 加载上一个上下文并检测包管理器 |
| **Pre-compact** | `PreCompact` | Saves state before context compaction |
| **预压缩** | `PreCompact` | 在上下文压缩前保存状态 |
| **Console.log audit** | `Stop` | Checks all modified files for `console.log` after each response |
| **Console.log 审计** | `Stop` | 每次响应后检查所有修改文件中的 `console.log` |
| **Session summary** | `Stop` | Persists session state when transcript path is available |
| **会话摘要** | `Stop` | 当有转录路径时持久化会话状态 |
| **Pattern extraction** | `Stop` | Evaluates session for extractable patterns (continuous learning) |
| **模式提取** | `Stop` | 评估会话中的可提取模式（持续学习） |
| **Cost tracker** | `Stop` | Emits lightweight run-cost telemetry markers |
| **成本追踪器** | `Stop` | 发出轻量级运行成本遥测标记 |
| **Session end marker** | `SessionEnd` | Lifecycle marker and cleanup log |
| **会话结束标记** | `SessionEnd` | 生命周期标记和清理日志 |

## Customizing Hooks
## 自定义 Hooks

### Disabling a Hook
### 禁用 Hook

Remove or comment out the hook entry in `hooks.json`. If installed as a plugin, override in your `~/.claude/settings.json`:
删除或注释掉 `hooks.json` 中的 hook 条目。如果作为插件安装，在 `~/.claude/settings.json` 中覆盖：

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write",
        "hooks": [],
        "description": "Override: allow all .md file creation"
      }
    ]
  }
}
```

### Runtime Hook Controls (Recommended)
### 运行时 Hook 控制（推荐）

Use environment variables to control hook behavior without editing `hooks.json`:
使用环境变量控制 hook 行为，无需编辑 `hooks.json`：

```bash
# minimal | standard | strict (default: standard)
export ECC_HOOK_PROFILE=standard

# Disable specific hook IDs (comma-separated)
export ECC_DISABLED_HOOKS="pre:bash:tmux-reminder,post:edit:typecheck"
```

Profiles:
配置文件：

- `minimal` — keep essential lifecycle and safety hooks only.
  `minimal` — 只保留基本的生命周期和安全 hooks。

- `standard` — default; balanced quality + safety checks.
  `standard` — 默认；平衡的质量 + 安全检查。

- `strict` — enables additional reminders and stricter guardrails.
  `strict` — 启用额外的提醒和更严格的保护措施。

### Writing Your Own Hook
### 编写自己的 Hook

Hooks are shell commands that receive tool input as JSON on stdin and must output JSON on stdout.
Hooks 是接收 stdin 上的 JSON 工具输入并必须在 stdout 上输出 JSON 的 shell 命令。

**Basic structure:**
**基本结构：**

```javascript
// my-hook.js
let data = '';
process.stdin.on('data', chunk => data += chunk);
process.stdin.on('end', () => {
  const input = JSON.parse(data);

  // Access tool info
  // 访问工具信息
  const toolName = input.tool_name;        // "Edit", "Bash", "Write", etc.
  const toolInput = input.tool_input;      // Tool-specific parameters
  const toolOutput = input.tool_output;    // Only available in PostToolUse

  // Warn (non-blocking): write to stderr
  // 警告（非阻塞）：写入 stderr
  console.error('[Hook] Warning message shown to Claude');

  // Block (PreToolUse only): exit with code 2
  // 阻止（仅限 PreToolUse）：退出码 2
  // process.exit(2);

  // Always output the original data to stdout
  // 始终将原始数据输出到 stdout
  console.log(data);
});
```

**Exit codes:**
**退出码：**

- `0` — Success (continue execution)
  `0` — 成功（继续执行）

- `2` — Block the tool call (PreToolUse only)
  `2` — 阻止工具调用（仅限 PreToolUse）

- Other non-zero — Error (logged but does not block)
  其他非零 — 错误（记录但不阻止）

### Hook Input Schema
### Hook 输入 Schema

```typescript
interface HookInput {
  tool_name: string;          // "Bash", "Edit", "Write", "Read", etc.
  tool_input: {
    command?: string;         // Bash: the command being run
    file_path?: string;       // Edit/Write/Read: target file
    old_string?: string;      // Edit: text being replaced
    new_string?: string;      // Edit: replacement text
    content?: string;         // Write: file content
  };
  tool_output?: {             // PostToolUse only
    output?: string;          // Command/tool output
  };
}
```

### Async Hooks
### 异步 Hooks

For hooks that should not block the main flow (e.g., background analysis):
对于不应阻塞主流程的 hooks（例如后台分析）：

```json
{
  "type": "command",
  "command": "node my-slow-hook.js",
  "async": true,
  "timeout": 30
}
```

Async hooks run in the background. They cannot block tool execution.
异步 hooks 在后台运行。它们不能阻止工具执行。

## Common Hook Recipes
## 常用 Hook 配方

### Warn about TODO comments
### 警告 TODO 注释

```json
{
  "matcher": "Edit",
  "hooks": [{
    "type": "command",
    "command": "node -e \"let d='';process.stdin.on('data',c=>d+=c);process.stdin.on('end',()=>{const i=JSON.parse(d);const ns=i.tool_input?.new_string||'';if(/TODO|FIXME|HACK/.test(ns)){console.error('[Hook] New TODO/FIXME added - consider creating an issue')}console.log(d)})\""
  }],
  "description": "Warn when adding TODO/FIXME comments"
}
```

### Block large file creation
### 阻止创建大文件

```json
{
  "matcher": "Write",
  "hooks": [{
    "type": "command",
    "command": "node -e \"let d='';process.stdin.on('data',c=>d+=c);process.stdin.on('end',()=>{const i=JSON.parse(d);const c=i.tool_input?.content||'';const lines=c.split('\\n').length;if(lines>800){console.error('[Hook] BLOCKED: File exceeds 800 lines ('+lines+' lines)');console.error('[Hook] Split into smaller, focused modules');process.exit(2)}console.log(d)})\""
  }],
  "description": "Block creation of files larger than 800 lines"
}
```

### Auto-format Python files with ruff
### 用 ruff 自动格式化 Python 文件

```json
{
  "matcher": "Edit",
  "hooks": [{
    "type": "command",
    "command": "node -e \"let d='';process.stdin.on('data',c=>d+=c);process.stdin.on('end',()=>{const i=JSON.parse(d);const p=i.tool_input?.file_path||'';if(/\\.py$/.test(p)){const{execFileSync}=require('child_process');try{execFileSync('ruff',['format',p],{stdio:'pipe'})}catch(e){}}console.log(d)})\""
  }],
  "description": "Auto-format Python files with ruff after edits"
}
```

### Require test files alongside new source files
### 新建源文件时要求测试文件

```json
{
  "matcher": "Write",
  "hooks": [{
    "type": "command",
    "command": "node -e \"const fs=require('fs');let d='';process.stdin.on('data',c=>d+=c);process.stdin.on('end',()=>{const i=JSON.parse(d);const p=i.tool_input?.file_path||'';if(/src\\/.*\\.(ts|js)$/.test(p)&&!/\\.test\\.|\\.spec\\./.test(p)){const testPath=p.replace(/\\.(ts|js)$/,'.test.$1');if(!fs.existsSync(testPath)){console.error('[Hook] No test file found for: '+p);console.error('[Hook] Expected: '+testPath);console.error('[Hook] Consider writing tests first (/tdd)')}}console.log(d)})\""
  }],
  "description": "Remind to create tests when adding new source files"
}
```

## Cross-Platform Notes
## 跨平台说明

Hook logic is implemented in Node.js scripts for cross-platform behavior on Windows, macOS, and Linux. A small number of shell wrappers are retained for continuous-learning observer hooks; those wrappers are profile-gated and have Windows-safe fallback behavior.
Hook 逻辑使用 Node.js 脚本实现，以在 Windows、macOS 和 Linux 上实现跨平台行为。保留了少量 shell 包装器用于持续学习观察者 hooks；这些包装器受配置文件控制，并具有 Windows 安全的回退行为。

## Related
## 相关内容

- [rules/common/hooks.md](../rules/common/hooks.md) — Hook architecture guidelines
  [rules/common/hooks.md](../rules/common/hooks.md) — Hook 架构指南

- [skills/strategic-compact/](../skills/strategic-compact/) — Strategic compaction skill
  [skills/strategic-compact/](../skills/strategic-compact/) — 策略性压缩技能

- [scripts/hooks/](../scripts/hooks/) — Hook script implementations
  [scripts/hooks/](../scripts/hooks/) — Hook 脚本实现
