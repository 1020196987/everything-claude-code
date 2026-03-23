---
name: plankton-code-quality
description: "Write-time code quality enforcement using Plankton — auto-formatting, linting, and Claude-powered fixes on every file edit via hooks."
description zh-CN: 使用 Plankton 进行写入时代码质量强制 — 通过 hooks 在每次文件编辑时自动格式化、lint 和 Claude 驱动的修复。
origin: community
---

# Plankton Code Quality Skill

## Plankton Code Quality Skill
## Plankton 代码质量技能

Integration reference for Plankton (credit: @alxfazio), a write-time code quality enforcement system for Claude Code. Plankton runs formatters and linters on every file edit via PostToolUse hooks, then spawns Claude subprocesses to fix violations the agent didn't catch.
Plankton（作者：@alxfazio）的集成参考，Cluade Code 的写入时代码质量强制系统。Plankton 通过 PostToolUse hooks 在每次文件编辑时运行格式化工具和 linter，然后启动 Claude 子进程来修复 agent 未捕获的违规。

## When to Use

## When to Use
## 何时使用

- You want automatic formatting and linting on every file edit (not just at commit time)
- 希望在每次文件编辑时自动格式化和 lint（而非仅在提交时）
- You need defense against agents modifying linter configs to pass instead of fixing code
- 需要防止 agent 修改 linter 配置来通过而非修复代码
- You want tiered model routing for fixes (Haiku for simple style, Sonnet for logic, Opus for types)
- 希望对修复进行分层模型路由（Haiku 处理简单样式，Sonnet 处理逻辑，Opus 处理类型）
- You work with multiple languages (Python, TypeScript, Shell, YAML, JSON, TOML, Markdown, Dockerfile)
- 使用多种语言（Python、TypeScript、Shell、YAML、JSON、TOML、Markdown、Dockerfile）

## How It Works

## How It Works
## 工作原理

### Three-Phase Architecture

### Three-Phase Architecture
### 三阶段架构

Every time Claude Code edits or writes a file, Plankton's `multi_linter.sh` PostToolUse hook runs:
每次 Claude Code 编辑或写入文件时，Plankton 的 `multi_linter.sh` PostToolUse hook 运行：

```
Phase 1: Auto-Format (Silent)
├─ Runs formatters (ruff format, biome, shfmt, taplo, markdownlint)
├─ Fixes 40-50% of issues silently
└─ No output to main agent

Phase 2: Collect Violations (JSON)
├─ Runs linters and collects unfixable violations
├─ Returns structured JSON: {line, column, code, message, linter}
└─ Still no output to main agent

Phase 3: Delegate + Verify
├─ Spawns claude -p subprocess with violations JSON
├─ Routes to model tier based on violation complexity:
│   ├─ Haiku: formatting, imports, style (E/W/F codes) — 120s timeout
│   ├─ Sonnet: complexity, refactoring (C901, PLR codes) — 300s timeout
│   └─ Opus: type system, deep reasoning (unresolved-attribute) — 600s timeout
├─ Re-runs Phase 1+2 to verify fixes
└─ Exit 0 if clean, Exit 2 if violations remain (reported to main agent)
```

### What the Main Agent Sees

### What the Main Agent Sees
### 主 agent 看到的内容

| Scenario | Agent sees | Hook exit |
| 场景 | agent 看到的内容 | Hook 退出码 |
|----------|-----------|-----------|
| No violations / 无违规 | Nothing / 无 | 0 |
| All fixed by subprocess / 子进程全部修复 | Nothing / 无 | 0 |
| Violations remain after subprocess / 子进程后仍有违规 | `[hook] N violation(s) remain` | 2 |
| Advisory (duplicates, old tooling) / 建议（重复、旧工具） | `[hook:advisory] ...` | 0 |

The main agent only sees issues the subprocess couldn't fix. Most quality problems are resolved transparently.
主 agent 只看到子进程无法修复的问题。大多数质量问题都被透明地解决了。

### Config Protection (Defense Against Rule-Gaming)

### Config Protection (Defense Against Rule-Gaming)
### 配置保护（防止规则博弈）

LLMs will modify `.ruff.toml` or `biome.json` to disable rules rather than fix code. Plankton blocks this with three layers:
LLM 会修改 `.ruff.toml` 或 `biome.json` 来禁用规则而非修复代码。Plankton 用三层阻止：

1. **PreToolUse hook** — `protect_linter_configs.sh` blocks edits to all linter configs before they happen
1. **PreToolUse hook** — `protect_linter_configs.sh` 在更改前阻止对所有 linter 配置的编辑
2. **Stop hook** — `stop_config_guardian.sh` detects config changes via `git diff` at session end
2. **Stop hook** — `stop_config_guardian.sh` 在会话结束时通过 `git diff` 检测配置更改
3. **Protected files list** — `.ruff.toml`, `biome.json`, `.shellcheckrc`, `.yamllint`, `.hadolint.yaml`, and more
3. **受保护文件列表** — `.ruff.toml`、`biome.json`、`.shellcheckrc`、`.yamllint`、`.hadolint.yaml` 等

### Package Manager Enforcement

### Package Manager Enforcement
### 包管理器强制

A PreToolUse hook on Bash blocks legacy package managers:
Bash 上的 PreToolUse hook 阻止遗留包管理器：
- `pip`, `pip3`, `poetry`, `pipenv` → Blocked (use `uv`)
- `pip`、`pip3`、`poetry`、`pipenv` → 被阻止（使用 `uv`）
- `npm`, `yarn`, `pnpm` → Blocked (use `bun`)
- `npm`、`yarn`、`pnpm` → 被阻止（使用 `bun`）
- Allowed exceptions: `npm audit`, `npm view`, `npm publish`
- 允许例外：`npm audit`、`npm view`、`npm publish`

## Setup

## Setup
## 设置

### Quick Start

### Quick Start
### 快速开始

```bash
# Clone Plankton into your project (or a shared location)
# Note: Plankton is by @alxfazio
# 将 Plankton 克隆到你的项目中（或共享位置）
# 注意：Plankton 由 @alxfazio 开发
git clone https://github.com/alexfazio/plankton.git
cd plankton

# Install core dependencies
# 安装核心依赖
brew install jaq ruff uv

# Install Python linters
# 安装 Python linter
uv sync --all-extras

# Start Claude Code — hooks activate automatically
# 启动 Claude Code — hooks 自动激活
claude
```

No install command, no plugin config. The hooks in `.claude/settings.json` are picked up automatically when you run Claude Code in the Plankton directory.
无需安装命令，无需插件配置。在 Plankton 目录中运行 Claude Code 时，`.claude/settings.json` 中的 hooks 会自动被加载。

### Per-Project Integration

### Per-Project Integration
### 每个项目的集成

To use Plankton hooks in your own project:
在你自己项目中使用 Plankton hooks：

1. Copy `.claude/hooks/` directory to your project
1. 将 `.claude/hooks/` 目录复制到你的项目
2. Copy `.claude/settings.json` hook configuration
2. 复制 `.claude/settings.json` hook 配置
3. Copy linter config files (`.ruff.toml`, `biome.json`, etc.)
3. 复制 linter 配置文件（`.ruff.toml`、`biome.json` 等）
4. Install the linters for your languages
4. 为你的语言安装 linter

### Language-Specific Dependencies

### Language-Specific Dependencies
### 语言特定依赖

| Language | Required | Optional |
| 语言 | 必需 | 可选 |
|----------|----------|----------|
| Python | `ruff`, `uv` | `ty` (types), `vulture` (dead code), `bandit` (security) |
| TypeScript/JS | `biome` | `oxlint`, `semgrep`, `knip` (dead exports) |
| Shell | `shellcheck`, `shfmt` | — |
| YAML | `yamllint` | — |
| Markdown | `markdownlint-cli2` | — |
| Dockerfile | `hadolint` (>= 2.12.0) | — |
| TOML | `taplo` | — |
| JSON | `jaq` | — |

## Pairing with ECC

## Pairing with ECC
## 与 ECC 配合使用

### Complementary, Not Overlapping

### Complementary, Not Overlapping
### 互补而非重叠

| Concern | ECC | Plankton |
| 关注点 | ECC | Plankton |
|---------|-----|----------|
| Code quality enforcement | PostToolUse hooks (Prettier, tsc) | PostToolUse hooks (20+ linters + subprocess fixes) |
| 代码质量强制 | PostToolUse hooks（Prettier, tsc） | PostToolUse hooks（20+ linter + 子进程修复） |
| Security scanning | AgentShield, security-reviewer agent | Bandit (Python), Semgrep (TypeScript) |
| 安全扫描 | AgentShield, security-reviewer agent | Bandit (Python), Semgrep (TypeScript) |
| Config protection | — | PreToolUse blocks + Stop hook detection |
| 配置保护 | — | PreToolUse 阻止 + Stop hook 检测 |
| Package manager | Detection + setup | Enforcement (blocks legacy PMs) |
| 包管理器 | 检测 + 设置 | 强制（阻止遗留 PM） |
| CI integration | — | Pre-commit hooks for git |
| CI 集成 | — | git 预提交 hooks |
| Model routing | Manual (`/model opus`) | Automatic (violation complexity → tier) |
| 模型路由 | 手动（`/model opus`） | 自动（按违规复杂度分级） |

### Recommended Combination

### Recommended Combination
### 推荐组合

1. Install ECC as your plugin (agents, skills, commands, rules)
1. 安装 ECC 作为你的插件（agents、skills、commands、rules）
2. Add Plankton hooks for write-time quality enforcement
2. 添加 Plankton hooks 进行写入时质量强制
3. Use AgentShield for security audits
3. 使用 AgentShield 进行安全审计
4. Use ECC's verification-loop as a final gate before PRs
4. 在 PR 前使用 ECC 的 verification-loop 作为最终关卡

### Avoiding Hook Conflicts

### Avoiding Hook Conflicts
### 避免 Hook 冲突

If running both ECC and Plankton hooks:
如果同时运行 ECC 和 Plankton hooks：

- ECC's Prettier hook and Plankton's biome formatter may conflict on JS/TS files
- ECC 的 Prettier hook 和 Plankton 的 biome 格式化工具可能在 JS/TS 文件上冲突
- Resolution: disable ECC's Prettier PostToolUse hook when using Plankton (Plankton's biome is more comprehensive)
- 解决方案：使用 Plankton 时禁用 ECC 的 Prettier PostToolUse hook（Plankton 的 biome 更全面）
- Both can coexist on different file types (ECC handles what Plankton doesn't cover)
- 两者可以在不同文件类型上共存（ECC 处理 Plankton 未覆盖的内容）

## Configuration Reference

## Configuration Reference
## 配置参考

Plankton's `.claude/hooks/config.json` controls all behavior:
Plankton 的 `.claude/hooks/config.json` 控制所有行为：

```json
{
  "languages": {
    "python": true,
    "shell": true,
    "yaml": true,
    "json": true,
    "toml": true,
    "dockerfile": true,
    "markdown": true,
    "typescript": {
      "enabled": true,
      "js_runtime": "auto",
      "biome_nursery": "warn",
      "semgrep": true
    }
  },
  "phases": {
    "auto_format": true,
    "subprocess_delegation": true
  },
  "subprocess": {
    "tiers": {
      "haiku":  { "timeout": 120, "max_turns": 10 },
      "sonnet": { "timeout": 300, "max_turns": 10 },
      "opus":   { "timeout": 600, "max_turns": 15 }
    },
    "volume_threshold": 5
  }
}
```

**Key settings:**
**关键设置：**
- Disable languages you don't use to speed up hooks
- 禁用你不使用的语言以加速 hooks
- `volume_threshold` — violations > this count auto-escalate to a higher model tier
- `volume_threshold` — 违规数超过此值自动升级到更高模型层
- `subprocess_delegation: false` — skip Phase 3 entirely (just report violations)
- `subprocess_delegation: false` — 完全跳过第 3 阶段（仅报告违规）

## Environment Overrides

## Environment Overrides
## 环境覆盖

| Variable | Purpose |
| 变量 | 用途 |
|----------|---------|
| `HOOK_SKIP_SUBPROCESS=1` | Skip Phase 3, report violations directly / 跳过第 3 阶段，直接报告违规 |
| `HOOK_SUBPROCESS_TIMEOUT=N` | Override tier timeout / 覆盖层超时时间 |
| `HOOK_DEBUG_MODEL=1` | Log model selection decisions / 记录模型选择决策 |
| `HOOK_SKIP_PM=1` | Bypass package manager enforcement / 绕过包管理器强制 |

## References

## References
## 参考资料

- Plankton (credit: @alxfazio)
- Plankton REFERENCE.md — Full architecture documentation (credit: @alxfazio)
- Plankton SETUP.md — Detailed installation guide (credit: @alxfazio)

## ECC v1.8 Additions

## ECC v1.8 Additions
## ECC v1.8 新增内容

### Copyable Hook Profile

### Copyable Hook Profile
### 可复制的 Hook 配置

Set strict quality behavior:
设置严格的质量行为：

```bash
export ECC_HOOK_PROFILE=strict
export ECC_QUALITY_GATE_FIX=true
export ECC_QUALITY_GATE_STRICT=true
```

### Language Gate Table

### Language Gate Table
### 语言门控表

- TypeScript/JavaScript: Biome preferred, Prettier fallback
- Python: Ruff format/check
- Go: gofmt

### Config Tamper Guard

### Config Tamper Guard
### 配置篡改防护

During quality enforcement, flag changes to config files in same iteration:
在质量强制期间，标记同一迭代中配置文件的更改：

- `biome.json`, `.eslintrc*`, `prettier.config*`, `tsconfig.json`, `pyproject.toml`
- `biome.json`、`.eslintrc*`、`prettier.config*`、`tsconfig.json`、`pyproject.toml`

If config is changed to suppress violations, require explicit review before merge.
如果配置被更改以压制违规，需要在合并前进行明确审查。

### CI Integration Pattern

### CI Integration Pattern
### CI 集成模式

Use the same commands in CI as local hooks:
在 CI 中使用与本地 hooks 相同的命令：

1. run formatter checks
1. 运行格式化工具检查
2. run lint/type checks
2. 运行 lint/类型检查
3. fail fast on strict mode
3. 在严格模式下快速失败
4. publish remediation summary
4. 发布修复摘要

### Health Metrics

### Health Metrics
### 健康指标

Track:
追踪：
- edits flagged by gates
- 被门控标记的编辑
- average remediation time
- 平均修复时间
- repeat violations by category
- 按类别重复违规
- merge blocks due to gate failures
- 由于门控失败导致的合并阻止
