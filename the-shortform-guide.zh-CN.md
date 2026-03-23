# The Shorthand Guide to Everything Claude Code
# Everything Claude Code 简明指南

![Header: Anthropic Hackathon Winner - Tips & Tricks for Claude Code](./assets/images/shortform/00-header.png)

---

**Been an avid Claude Code user since the experimental rollout in Feb, and won the Anthropic x Forum Ventures hackathon with [zenith.chat](https://zenith.chat) alongside [@DRodriguezFX](https://x.com/DRodriguezFX) - completely using Claude Code.**
**自 2 月份实验性推出以来一直是狂热的 Claude Code 用户，并与 [@DRodriguezFX](https://x.com/DRodriguezFX) 一起使用 Claude Code 赢得了 Anthropic x Forum Ventures hackathon，构建了 [zenith.chat](https://zenith.chat)**

Here's my complete setup after 10 months of daily use: skills, hooks, subagents, MCPs, plugins, and what actually works.
以下是我 10 个月日常使用后的完整设置：skills、hooks、子代理、MCPs、插件，以及真正有效的方法。

---

## Skills and Commands
## Skills 和 Commands

Skills operate like rules, constricted to certain scopes and workflows. They're shorthand to prompts when you need to execute a particular workflow.
Skills 的运作方式类似于规则，限制在某些范围和工作流中。当您需要执行特定工作流时，它们是 prompts 的简写。

After a long session of coding with Opus 4.5, you want to clean out dead code and loose .md files? Run `/refactor-clean`. Need testing? `/tdd`, `/e2e`, `/test-coverage`. Skills can also include codemaps - a way for Claude to quickly navigate your codebase without burning context on exploration.
在用 Opus 4.5 进行长时间的编码会话后，您想清理死代码和松散的 .md 文件？运行 `/refactor-clean`。需要测试？`/tdd`、`/e2e`、`/test-coverage`。Skills 还可以包含 codemaps——一种让 Claude 快速导航您的代码库而不会在探索中消耗上下文的方式。

![Terminal showing chained commands](./assets/images/shortform/02-chaining-commands.jpeg)
*Chaining commands together*
*链接命令*

Commands are skills executed via slash commands. They overlap but are stored differently:
Commands 是通过斜杠命令执行的 skills。它们重叠但存储方式不同：

- **Skills**: `~/.claude/skills/` - broader workflow definitions
  **Skills**: `~/.claude/skills/` - 更广泛的工作流定义
- **Commands**: `~/.claude/commands/` - quick executable prompts
  **Commands**: `~/.claude/commands/` - 快速可执行 prompts

```bash
# Example skill structure
~/.claude/skills/
  pmx-guidelines.md      # Project-specific patterns
  coding-standards.md    # Language best practices
  tdd-workflow/          # Multi-file skill with README.md
  security-review/       # Checklist-based skill
# 示例 skill 结构
~/.claude/skills/
  pmx-guidelines.md      # 项目特定模式
  coding-standards.md    # 语言最佳实践
  tdd-workflow/          # 多文件 skill 带有 README.md
  security-review/       # 基于检查清单的 skill
```

---

## Hooks
## Hooks

Hooks are trigger-based automations that fire on specific events. Unlike skills, they're constricted to tool calls and lifecycle events.
Hooks 是基于触发器的自动化，在特定事件上触发。与 skills 不同，它们限制在工具调用和生命周期事件上。

**Hook Types:**
**Hook 类型：**

1. **PreToolUse** - Before a tool executes (validation, reminders)
1. **PreToolUse** - 工具执行前（验证、提醒）
2. **PostToolUse** - After a tool finishes (formatting, feedback loops)
2. **PostToolUse** - 工具完成后（格式化、反馈循环）
3. **UserPromptSubmit** - When you send a message
3. **UserPromptSubmit** - 当您发送消息时
4. **Stop** - When Claude finishes responding
4. **Stop** - Claude 完成响应时
5. **PreCompact** - Before context compaction
5. **PreCompact** - 上下文压缩前
6. **Notification** - Permission requests
6. **Notification** - 权限请求

**Example: tmux reminder before long-running commands**
**示例：长时间运行命令前的 tmux 提醒**

```json
{
  "PreToolUse": [
    {
      "matcher": "tool == \"Bash\" && tool_input.command matches \"(npm|pnpm|yarn|cargo|pytest)\"",
      "hooks": [
        {
          "type": "command",
          "command": "if [ -z \"$TMUX\" ]; then echo '[Hook] Consider tmux for session persistence' >&2; fi"
        }
      ]
    }
  ]
}
```

![PostToolUse hook feedback](./assets/images/shortform/03-posttooluse-hook.png)
*Example of what feedback you get in Claude Code, while running a PostToolUse hook*
*运行 PostToolUse hook 时收到的反馈示例*

**Pro tip:** Use the `hookify` plugin to create hooks conversationally instead of writing JSON manually. Run `/hookify` and describe what you want.
**专业提示：** 使用 `hookify` 插件以对话方式创建 hooks，而不是手动编写 JSON。运行 `/hookify` 并描述您想要的。

---

## Subagents
## 子代理

Subagents are processes your orchestrator (main Claude) can delegate tasks to with limited scopes. They can run in background or foreground, freeing up context for the main agent.
子代理是您的编排器（主 Claude）可以委托有限范围任务给它们的进程。它们可以在后台或前台运行，为主代理释放上下文。

Subagents work nicely with skills - a subagent capable of executing a subset of your skills can be delegated tasks and use those skills autonomously. They can also be sandboxed with specific tool permissions.
子代理与 skills 配合得很好——能够执行 skills 子集的子代理可以被委托任务并自主使用这些 skills。它们也可以用特定工具权限进行沙盒化。

```bash
# Example subagent structure
~/.claude/agents/
  planner.md           # Feature implementation planning
  architect.md         # System design decisions
  tdd-guide.md         # Test-driven development
  code-reviewer.md     # Quality/security review
  security-reviewer.md # Vulnerability analysis
  build-error-resolver.md
  e2e-runner.md
  refactor-cleaner.md
# 示例子代理结构
~/.claude/agents/
  planner.md           # 功能实现规划
  architect.md         # 系统设计决策
  tdd-guide.md         # 测试驱动开发
  code-reviewer.md     # 质量/安全审查
  security-reviewer.md # 漏洞分析
  build-error-resolver.md
  e2e-runner.md
  refactor-cleaner.md
```

Configure allowed tools, MCPs, and permissions per subagent for proper scoping.
为每个子代理配置允许的工具、MCPs 和权限以进行适当的作用域划分。

---

## Rules and Memory
## 规则和内存

Your `.rules` folder holds `.md` files with best practices Claude should ALWAYS follow. Two approaches:
您的 `.rules` 文件夹包含 Claude 应始终遵循的 `.md` 文件最佳实践。两种方法：

1. **Single CLAUDE.md** - Everything in one file (user or project level)
1. **单个 CLAUDE.md** - 一切在一个文件中（用户或项目级）
2. **Rules folder** - Modular `.md` files grouped by concern
2. **规则文件夹** - 按关注点分组的模块化 `.md` 文件

```bash
~/.claude/rules/
  security.md      # No hardcoded secrets, validate inputs
  coding-style.md  # Immutability, file organization
  testing.md       # TDD workflow, 80% coverage
  git-workflow.md  # Commit format, PR process
  agents.md        # When to delegate to subagents
  performance.md   # Model selection, context management
```
```bash
~/.claude/rules/
  security.md      # 无硬编码 secrets，验证输入
  coding-style.md  # 不可变性，文件组织
  testing.md       # TDD 工作流，80% 覆盖率
  git-workflow.md  # 提交格式，PR 流程
  agents.md        # 何时委托给子代理
  performance.md   # 模型选择，上下文管理
```

**Example rules:**
**示例规则：**

- No emojis in codebase
- 代码库中无 emoji
- Refrain from purple hues in frontend
- 前端避免紫色色调
- Always test code before deployment
- 部署前始终测试代码
- Prioritize modular code over mega-files
- 优先模块化代码而非大型文件
- Never commit console.logs
- 永不提交 console.log

---

## MCPs (Model Context Protocol)
## MCPs（模型上下文协议）

MCPs connect Claude to external services directly. Not a replacement for APIs - it's a prompt-driven wrapper around them, allowing more flexibility in navigating information.
MCPs 将 Claude 直接连接到外部服务。不是 API 的替代品——它是围绕它们的 prompt 驱动包装器，在导航信息方面提供更多灵活性。

**Example:** Supabase MCP lets Claude pull specific data, run SQL directly upstream without copy-paste. Same for databases, deployment platforms, etc.
**示例：** Supabase MCP 让 Claude 直接拉取特定数据，在上游运行 SQL，无需复制粘贴。数据库和部署平台等也是如此。

![Supabase MCP listing tables](./assets/images/shortform/04-supabase-mcp.jpeg)
*Example of the Supabase MCP listing the tables within the public schema*
*Supabase MCP 列出公共模式中的表示例*

**Chrome in Claude:** is a built-in plugin MCP that lets Claude autonomously control your browser - clicking around to see how things work.
**Chrome in Claude：** 是一个内置插件 MCP，让 Claude 可以自主控制您的浏览器——点击查看事物如何工作。

**CRITICAL: Context Window Management**
**关键：上下文窗口管理**

Be picky with MCPs. I keep all MCPs in user config but **disable everything unused**. Navigate to `/plugins` and scroll down or run `/mcp`.
对 MCPs 要有选择性。我将所有 MCPs 保持在用户配置中但**禁用所有未使用的**。导航到 `/plugins` 并向下滚动或运行 `/mcp`。

![/plugins interface](./assets/images/shortform/05-plugins-interface.jpeg)
*Using /plugins to navigate to MCPs to see which ones are currently installed and their status*
*使用 /plugins 导航到 MCPs 查看当前安装的及其状态*

Your 200k context window before compacting might only be 70k with too many tools enabled. Performance degrades significantly.
在压缩之前您的 200k 上下文窗口可能在使用太多启用工具时只有 70k。性能显著下降。

**Rule of thumb:** Have 20-30 MCPs in config, but keep under 10 enabled / under 80 tools active.
**经验法则：** 在配置中有 20-30 个 MCPs，但保持启用少于 10 个/少于 80 个活动工具。

```bash
# Check enabled MCPs
/mcp
# 检查启用的 MCPs
/mcp

# Disable unused ones in ~/.claude.json under projects.disabledMcpServers
# 在 ~/.claude.json 中的 projects.disabledMcpServers 下禁用未使用的
```

---

## Plugins
## 插件

Plugins package tools for easy installation instead of tedious manual setup. A plugin can be a skill + MCP combined, or hooks/tools bundled together.
插件将工具打包以便轻松安装，而不是繁琐的手动设置。插件可以是 skill + MCP 组合，或捆绑在一起的 hooks/工具。

**Installing plugins:**
**安装插件：**

```bash
# Add a marketplace
# mgrep plugin by @mixedbread-ai
claude plugin marketplace add https://github.com/mixedbread-ai/mgrep
# 添加市场
# mgrep 插件 by @mixedbread-ai
claude plugin marketplace add https://github.com/mixedbread-ai/mgrep

# Open Claude, run /plugins, find new marketplace, install from there
# 打开 Claude，运行 /plugins，找到新市场，从那里安装
```

![Marketplaces tab showing mgrep](./assets/images/shortform/06-marketplaces-mgrep.jpeg)
*Displaying the newly installed Mixedbread-Grep marketplace*
*显示新安装的 Mixedbread-Grep marketplace*

**LSP Plugins** are particularly useful if you run Claude Code outside editors frequently. Language Server Protocol gives Claude real-time type checking, go-to-definition, and intelligent completions without needing an IDE open.
**LSP 插件** 如果您经常在编辑器外运行 Claude Code，它们特别有用。语言服务器协议让 Claude 无需打开 IDE 就能获得实时类型检查、跳转到定义和智能补全。

```bash
# Enabled plugins example
typescript-lsp@claude-plugins-official  # TypeScript intelligence
pyright-lsp@claude-plugins-official     # Python type checking
hookify@claude-plugins-official         # Create hooks conversationally
mgrep@Mixedbread-Grep                   # Better search than ripgrep
# 启用的插件示例
typescript-lsp@claude-plugins-official  # TypeScript 智能
pyright-lsp@claude-plugins-official     # Python 类型检查
hookify@claude-plugins-official         # 对话式创建 hooks
mgrep@Mixedbread-Grep                   # 比 ripgrep 更好的搜索
```

Same warning as MCPs - watch your context window.
与 MCPs 相同的警告——注意您的上下文窗口。

---

## Tips and Tricks
## 技巧和窍门

### Keyboard Shortcuts
### 键盘快捷键

- `Ctrl+U` - Delete entire line (faster than backspace spam)
- `Ctrl+U` - 删除整行（比退格键更快）
- `!` - Quick bash command prefix
- `!` - 快速 bash 命令前缀
- `@` - Search for files
- `@` - 搜索文件
- `/` - Initiate slash commands
- `/` - 启动斜杠命令
- `Shift+Enter` - Multi-line input
- `Shift+Enter` - 多行输入
- `Tab` - Toggle thinking display
- `Tab` - 切换思考显示
- `Esc Esc` - Interrupt Claude / restore code
- `Esc Esc` - 中断 Claude / 恢复代码

### Parallel Workflows
### 并行工作流

- **Fork** (`/fork`) - Fork conversations to do non-overlapping tasks in parallel instead of spamming queued messages
  **Fork** (`/fork`) - 分叉对话以并行执行不重叠的任务，而不是发送排队的消息
- **Git Worktrees** - For overlapping parallel Claudes without conflicts. Each worktree is an independent checkout
  **Git Worktrees** - 对于没有冲突的重叠并行 Claudes。每个 worktree 是一个独立的 checkout

```bash
git worktree add ../feature-branch feature-branch
# Now run separate Claude instances in each worktree
git worktree add ../feature-branch feature-branch
# 现在在每个 worktree 中运行单独的 Claude 实例
```

### tmux for Long-Running Commands
### 用于长时间运行命令的 tmux

Stream and watch logs/bash processes Claude runs:
流式传输和监视 Claude 运行的日志/bash 进程：

https://github.com/user-attachments/assets/shortform/07-tmux-video.mp4

```bash
tmux new -s dev
# Claude runs commands here, you can detach and reattach
tmux attach -t dev
```
```bash
tmux new -s dev
# Claude 在这里运行命令，您可以分离和重新附加
tmux attach -t dev
```

### mgrep > grep
### mgrep > grep

`mgrep` is a significant improvement from ripgrep/grep. Install via plugin marketplace, then use the `/mgrep` skill. Works with both local search and web search.
`mgrep` 相比 ripgrep/grep 是一个显著的改进。通过 plugin marketplace 安装，然后使用 `/mgrep` skill。支持本地搜索和网络搜索。

```bash
mgrep "function handleSubmit"  # Local search
mgrep --web "Next.js 15 app router changes"  # Web search
```
```bash
mgrep "function handleSubmit"  # 本地搜索
mgrep --web "Next.js 15 app router changes"  # 网络搜索
```

### Other Useful Commands
### 其他有用命令

- `/rewind` - Go back to a previous state
- `/rewind` - 返回到先前状态
- `/statusline` - Customize with branch, context %, todos
- `/statusline` - 自定义分支、上下文百分比、待办事项
- `/checkpoints` - File-level undo points
- `/checkpoints` - 文件级撤销点
- `/compact` - Manually trigger context compaction
- `/compact` - 手动触发上下文压缩

### GitHub Actions CI/CD
### GitHub Actions CI/CD

Set up code review on your PRs with GitHub Actions. Claude can review PRs automatically when configured.
设置 PRs 的代码审查。配置后 Claude 可以自动审查 PRs。

![Claude bot approving a PR](./assets/images/shortform/08-github-pr-review.jpeg)
*Claude approving a bug fix PR*
*Claude 批准错误修复 PR*

### Sandboxing
### 沙盒化

Use sandbox mode for risky operations - Claude runs in restricted environment without affecting your actual system.
对风险操作使用沙盒模式—— Claude 在受限环境中运行，不会影响您的实际系统。

---

## On Editors
## 关于编辑器

Your editor choice significantly impacts Claude Code workflow. While Claude Code works from any terminal, pairing it with a capable editor unlocks real-time file tracking, quick navigation, and integrated command execution.
编辑器选择显著影响 Claude Code 工作流。虽然 Claude Code 从任何终端都能工作，但将其与 capable 编辑器配对可以解锁实时文件跟踪、快速导航和集成命令执行。

### Zed (My Preference)
### Zed（我的偏好）

I use [Zed](https://zed.dev) - written in Rust, so it's genuinely fast. Opens instantly, handles massive codebases without breaking a sweat, and barely touches system resources.
我使用 [Zed](https://zed.dev) —— 用 Rust 编写，所以它真的很快。立即打开，处理大型代码库而不费力，几乎不占用系统资源。

**Why Zed + Claude Code is a great combo:**
**为什么 Zed + Claude Code 是一个很棒的组合：**

- **Speed** - Rust-based performance means no lag when Claude is rapidly editing files. Your editor keeps up
  **速度** - 基于 Rust 的性能意味着当 Claude 快速编辑文件时没有延迟。您的编辑器跟得上
- **Agent Panel Integration** - Zed's Claude integration lets you track file changes in real-time as Claude edits. Jump between files Claude references without leaving the editor
  **Agent Panel 集成** - Zed 的 Claude 集成让您可以实时跟踪 Claude 编辑的文件更改。在 Claude 引用的文件之间跳转而不离开编辑器
- **CMD+Shift+R Command Palette** - Quick access to all your custom slash commands, debuggers, build scripts in a searchable UI
  **CMD+Shift+R 命令面板** - 快速访问您所有自定义斜杠命令、调试器、构建脚本，在可搜索的 UI 中
- **Minimal Resource Usage** - Won't compete with Claude for RAM/CPU during heavy operations. Important when running Opus
  **最小资源使用** - 不会与 Claude 争夺重操作期间的 RAM/CPU。运行 Opus 时很重要
- **Vim Mode** - Full vim keybindings if that's your thing
  **Vim 模式** - 如果您喜欢的话，完全的 vim 键绑定

![Zed Editor with custom commands](./assets/images/shortform/09-zed-editor.jpeg)
*Zed Editor with custom commands dropdown using CMD+Shift+R. Following mode shown as the bullseye in the bottom right.*
*Zed Editor 使用 CMD+Shift+R 的自定义命令下拉菜单。以下模式显示为右下角的靶心。*

**Editor-Agnostic Tips:**
**与编辑器无关的提示：**

1. **Split your screen** - Terminal with Claude Code on one side, editor on the other
1. **分屏** - 一边是带有 Claude Code 的终端，另一边是编辑器
2. **Ctrl + G** - quickly open the file Claude is currently working on in Zed
2. **Ctrl + G** - 快速打开 Claude 当前在 Zed 中处理的文件
3. **Auto-save** - Enable autosave so Claude's file reads are always current
3. **自动保存** - 启用自动保存，以便 Claude 的文件读取始终是最新的
4. **Git integration** - Use editor's git features to review Claude's changes before committing
4. **Git 集成** - 使用编辑器的 git 功能在提交前审查 Claude 的更改
5. **File watchers** - Most editors auto-reload changed files, verify this is enabled
5. **文件监视器** - 大多数编辑器会自动重新加载更改的文件，验证此功能已启用

### VSCode / Cursor
### VSCode / Cursor

This is also a viable choice and works well with Claude Code. You can use it in either terminal format, with automatic sync with your editor using `\ide` enabling LSP functionality (somewhat redundant with plugins now). Or you can opt for the extension which is more integrated with the Editor and has a matching UI.
这也是一个可行的选择，与 Claude Code 配合良好。您可以使用任一终端格式，使用 `\ide` 启用自动同步到您的编辑器，启用 LSP 功能（现在与插件有些冗余）。或者您可以选择更深入集成的扩展，UI 相匹配。

![VS Code Claude Code Extension](./assets/images/shortform/10-vscode-extension.jpeg)
*The VS Code extension provides a native graphical interface for Claude Code, integrated directly into your IDE.*
*VS Code 扩展为 Claude Code 提供了原生图形界面，直接集成到您的 IDE 中。*

---

## My Setup
## 我的设置

### Plugins
### 插件

**Installed:** (I usually only have 4-5 of these enabled at a time)
**已安装：**（我通常一次只启用 4-5 个）

```markdown
ralph-wiggum@claude-code-plugins       # Loop automation
frontend-design@claude-code-plugins    # UI/UX patterns
commit-commands@claude-code-plugins    # Git workflow
security-guidance@claude-code-plugins  # Security checks
pr-review-toolkit@claude-code-plugins  # PR automation
typescript-lsp@claude-plugins-official # TS intelligence
hookify@claude-plugins-official        # Hook creation
code-simplifier@claude-plugins-official
feature-dev@claude-code-plugins
explanatory-output-style@claude-code-plugins
code-review@claude-code-plugins
context7@claude-plugins-official       # Live documentation
pyright-lsp@claude-plugins-official    # Python types
mgrep@Mixedbread-Grep                  # Better search
```
```markdown
ralph-wiggum@claude-code-plugins       # 循环自动化
frontend-design@claude-code-plugins    # UI/UX 模式
commit-commands@claude-code-plugins    # Git 工作流
security-guidance@claude-code-plugins  # 安全检查
pr-review-toolkit@claude-code-plugins  # PR 自动化
typescript-lsp@claude-plugins-official # TS 智能
hookify@claude-plugins-official        # Hook 创建
code-simplifier@claude-plugins-official
feature-dev@claude-code-plugins
explanatory-output-style@claude-code-plugins
code-review@claude-code-plugins
context7@claude-plugins-official       # 实时文档
pyright-lsp@claude-plugins-official    # Python 类型
mgrep@Mixedbread-Grep                  # 更好的搜索
```

### MCP Servers
### MCP 服务器

**Configured (User Level):**
**已配置（用户级）：**

```json
{
  "github": { "command": "npx", "args": ["-y", "@modelcontextprotocol/server-github"] },
  "firecrawl": { "command": "npx", "args": ["-y", "firecrawl-mcp"] },
  "supabase": {
    "command": "npx",
    "args": ["-y", "@supabase/mcp-server-supabase@latest", "--project-ref=YOUR_REF"]
  },
  "memory": { "command": "npx", "args": ["-y", "@modelcontextprotocol/server-memory"] },
  "sequential-thinking": {
    "command": "npx",
    "args": ["-y", "@modelcontextprotocol/server-sequential-thinking"]
  },
  "vercel": { "type": "http", "url": "https://mcp.vercel.com" },
  "railway": { "command": "npx", "args": ["-y", "@railway/mcp-server"] },
  "cloudflare-docs": { "type": "http", "url": "https://docs.mcp.cloudflare.com/mcp" },
  "cloudflare-workers-bindings": {
    "type": "http",
    "url": "https://bindings.mcp.cloudflare.com/mcp"
  },
  "clickhouse": { "type": "http", "url": "https://mcp.clickhouse.cloud/mcp" },
  "AbletonMCP": { "command": "uvx", "args": ["ableton-mcp"] },
  "magic": { "command": "npx", "args": ["-y", "@magicuidesign/mcp@latest"] }
}
```

This is the key - I have 14 MCPs configured but only ~5-6 enabled per project. Keeps context window healthy.
关键是——我配置了 14 个 MCPs，但每个项目只启用约 5-6 个。保持上下文窗口健康。

### Key Hooks
### 关键 Hooks

```json
{
  "PreToolUse": [
    { "matcher": "npm|pnpm|yarn|cargo|pytest", "hooks": ["tmux reminder"] },
    { "matcher": "Write && .md file", "hooks": ["block unless README/CLAUDE"] },
    { "matcher": "git push", "hooks": ["open editor for review"] }
  ],
  "PostToolUse": [
    { "matcher": "Edit && .ts/.tsx/.js/.jsx", "hooks": ["prettier --write"] },
    { "matcher": "Edit && .ts/.tsx", "hooks": ["tsc --noEmit"] },
    { "matcher": "Edit", "hooks": ["grep console.log warning"] }
  ],
  "Stop": [
    { "matcher": "*", "hooks": ["check modified files for console.log"] }
  ]
}
```

### Custom Status Line
### 自定义状态行

Shows user, directory, git branch with dirty indicator, context remaining %, model, time, and todo count:
显示用户、目录、带脏指示器的 git 分支、剩余上下文百分比、模型、时间和待办数量：

![Custom status line](./assets/images/shortform/11-statusline.jpeg)
*Example statusline in my Mac root directory*
*我的 Mac 根目录中的示例状态行*

```
affoon:~ ctx:65% Opus 4.5 19:52
▌▌ plan mode on (shift+tab to cycle)
```

### Rules Structure
### 规则结构

```
~/.claude/rules/
  security.md      # Mandatory security checks
  coding-style.md  # Immutability, file size limits
  testing.md       # TDD, 80% coverage
  git-workflow.md  # Conventional commits
  agents.md        # Subagent delegation rules
  patterns.md      # API response formats
  performance.md   # Model selection (Haiku vs Sonnet vs Opus)
  hooks.md         # Hook documentation
```
```
~/.claude/rules/
  security.md      # 强制性安全检查
  coding-style.md  # 不可变性，文件大小限制
  testing.md       # TDD, 80% 覆盖率
  git-workflow.md  # 常规提交
  agents.md        # 子代理委托规则
  patterns.md      # API 响应格式
  performance.md   # 模型选择 (Haiku vs Sonnet vs Opus)
  hooks.md         # Hook 文档
```

### Subagents
### 子代理

```
~/.claude/agents/
  planner.md           # Break down features
  architect.md         # System design
  tdd-guide.md         # Write tests first
  code-reviewer.md     # Quality review
  security-reviewer.md # Vulnerability scan
  build-error-resolver.md
  e2e-runner.md        # Playwright tests
  refactor-cleaner.md  # Dead code removal
  doc-updater.md       # Keep docs synced
```
```
~/.claude/agents/
  planner.md           # 分解功能
  architect.md         # 系统设计
  tdd-guide.md         # 先写测试
  code-reviewer.md     # 质量审查
  security-reviewer.md # 漏洞扫描
  build-error-resolver.md
  e2e-runner.md        # Playwright 测试
  refactor-cleaner.md  # 死代码删除
  doc-updater.md       # 保持文档同步
```

---

## Key Takeaways
## 关键要点

1. **Don't overcomplicate** - treat configuration like fine-tuning, not architecture
1. **不要过于复杂** - 将配置视为微调，而不是架构
2. **Context window is precious** - disable unused MCPs and plugins
2. **上下文窗口是宝贵的** - 禁用未使用的 MCPs 和插件
3. **Parallel execution** - fork conversations, use git worktrees
3. **并行执行** - 分叉对话，使用 git worktrees
4. **Automate the repetitive** - hooks for formatting, linting, reminders
4. **自动化重复内容** - hooks 用于格式化、linting、提醒
5. **Scope your subagents** - limited tools = focused execution
5. **限制您的子代理** - 有限的工具 = 专注执行

---

## References
## 参考资料

- [Plugins Reference](https://code.claude.com/docs/en/plugins-reference)
- [Hooks Documentation](https://code.claude.com/docs/en/hooks)
- [Checkpointing](https://code.claude.com/docs/en/checkpointing)
- [Interactive Mode](https://code.claude.com/docs/en/interactive-mode)
- [Memory System](https://code.claude.com/docs/en/memory)
- [Subagents](https://code.claude.com/docs/en/sub-agents)
- [MCP Overview](https://code.claude.com/docs/en/mcp-overview)
- [Plugins 参考](https://code.claude.com/docs/en/plugins-reference)
- [Hooks 文档](https://code.claude.com/docs/en/hooks)
- [Checkpointing](https://code.claude.com/docs/en/checkpointing)
- [Interactive Mode](https://code.claude.com/docs/en/interactive-mode)
- [Memory System](https://code.claude.com/docs/en/memory)
- [Subagents](https://code.claude.com/docs/en/sub-agents)
- [MCP Overview](https://code.claude.com/docs/en/mcp-overview)

---

**Note:** This is a subset of detail. See the [Longform Guide](./the-longform-guide.md) for advanced patterns.
**注：** 这是细节的一个子集。参见 [详细指南](./the-longform-guide.md) 了解高级模式。

---

*Won the Anthropic x Forum Ventures hackathon in NYC building [zenith.chat](https://zenith.chat) with [@DRodriguezFX](https://x.com/DRodriguezFX)*
*在纽约与 [@DRodriguezFX](https://x.com/DRodriguezFX) 一起构建 [zenith.chat](https://zenith.chat) 赢得了 Anthropic x Forum Ventures hackathon*
