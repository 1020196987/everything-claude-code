---
name: security-scan
description: Scan your Claude Code configuration (.claude/ directory) for security vulnerabilities, misconfigurations, and injection risks using AgentShield. Checks CLAUDE.md, settings.json, MCP servers, hooks, and agent definitions.
description zh-CN: 使用 AgentShield 扫描您的 Claude Code 配置（.claude/ 目录）中的安全漏洞、错误配置和注入风险。检查 CLAUDE.md、 settings.json、 MCP 服务器、 hooks 和 agent 定义。
origin: ECC
---

# Security Scan Skill
# 安全扫描技能

Audit your Claude Code configuration for security issues using [AgentShield](https://github.com/affaan-m/agentshield).
使用 [AgentShield](https://github.com/affaan-m/agentshield) 审计您的 Claude Code 配置是否存在安全问题。

## When to Activate
## 何时激活

- Setting up a new Claude Code project
- 设置新的 Claude Code 项目
- After modifying `.claude/settings.json`, `CLAUDE.md`, or MCP configs
- 修改 `.claude/settings.json`、 `CLAUDE.md` 或 MCP 配置后
- Before committing configuration changes
- 提交配置更改前
- When onboarding to a new repository with existing Claude Code configs
- 接入具有现有 Claude Code 配置的新仓库时
- Periodic security hygiene checks
- 定期安全卫生检查

## What It Scans
## 扫描内容

| File | Checks |
|------|--------|
| `CLAUDE.md` | Hardcoded secrets, auto-run instructions, prompt injection patterns |
| `settings.json` | Overly permissive allow lists, missing deny lists, dangerous bypass flags |
| `mcp.json` | Risky MCP servers, hardcoded env secrets, npx supply chain risks |
| `hooks/` | Command injection via interpolation, data exfiltration, silent error suppression |
| `agents/*.md` | Unrestricted tool access, prompt injection surface, missing model specs |

| 文件 | 检查项 |
|------|--------|
| `CLAUDE.md` | 硬编码的机密、自动运行指令、提示注入模式 |
| `settings.json` | 过度宽松的允许列表、缺失的拒绝列表、危险的绕过标志 |
| `mcp.json` | 有风险的 MCP 服务器、硬编码的环境机密、npx 供应链风险 |
| `hooks/` | 通过插值的命令注入、数据泄露、静默错误抑制 |
| `agents/*.md` | 无限制的工具访问、提示注入面、缺失的模型规格 |

## Prerequisites
## 先决条件

AgentShield must be installed. Check and install if needed:
AgentShield 必须已安装。如有需要请检查并安装：

```bash
# Check if installed
npx ecc-agentshield --version

# Install globally (recommended)
npm install -g ecc-agentshield

# Or run directly via npx (no install needed)
npx ecc-agentshield scan .
```

```bash
# 检查是否已安装
npx ecc-agentshield --version

# 全局安装（推荐）
npm install -g ecc-agentshield

# 或直接通过 npx 运行（无需安装）
npx ecc-agentshield scan .
```

## Usage
## 使用方法

### Basic Scan
### 基本扫描

Run against the current project's `.claude/` directory:
对当前项目的 `.claude/` 目录运行扫描：

```bash
# Scan current project
npx ecc-agentshield scan

# Scan a specific path
npx ecc-agentshield scan --path /path/to/.claude

# Scan with minimum severity filter
npx ecc-agentshield scan --min-severity medium
```

```bash
# 扫描当前项目
npx ecc-agentshield scan

# 扫描特定路径
npx ecc-agentshield scan --path /path/to/.claude

# 按最低严重级别过滤扫描
npx ecc-agentshield scan --min-severity medium
```

### Output Formats
### 输出格式

```bash
# Terminal output (default) — colored report with grade
npx ecc-agentshield scan

# JSON — for CI/CD integration
npx ecc-agentshield scan --format json

# Markdown — for documentation
npx ecc-agentshield scan --format markdown

# HTML — self-contained dark-theme report
npx ecc-agentshield scan --format html > security-report.html
```

```bash
# 终端输出（默认）— 带评级的彩色报告
npx ecc-agentshield scan

# JSON — 用于 CI/CD 集成
npx ecc-agentshield scan --format json

# Markdown — 用于文档
npx ecc-agentshield scan --format markdown

# HTML — 独立的暗色主题报告
npx ecc-agentshield scan --format html > security-report.html
```

### Auto-Fix
### 自动修复

Apply safe fixes automatically (only fixes marked as auto-fixable):
自动应用安全修复（仅修复标记为可自动修复的问题）：

```bash
npx ecc-agentshield scan --fix
```

This will:
这将：

- Replace hardcoded secrets with environment variable references
- 用环境变量引用替换硬编码的机密
- Tighten wildcard permissions to scoped alternatives
- 将通配符权限收紧到作用域替代方案
- Never modify manual-only suggestions
- 永远不会修改仅手动建议

### Opus 4.6 Deep Analysis
### Opus 4.6 深度分析

Run the adversarial three-agent pipeline for deeper analysis:
运行对抗性三 Agent 管道进行深度分析：

```bash
# Requires ANTHROPIC_API_KEY
export ANTHROPIC_API_KEY=your-key
npx ecc-agentshield scan --opus --stream
```

```bash
# 需要 ANTHROPIC_API_KEY
export ANTHROPIC_API_KEY=your-key
npx ecc-agentshield scan --opus --stream
```

This runs:
这将运行：

1. **Attacker (Red Team)** — finds attack vectors
1. **攻击者（红队）** — 寻找攻击向量
2. **Defender (Blue Team)** — recommends hardening
2. **防御者（蓝队）** — 建议加固
3. **Auditor (Final Verdict)** — synthesizes both perspectives
3. **审计员（最终裁决）** — 综合两种观点

### Initialize Secure Config
### 初始化安全配置

Scaffold a new secure `.claude/` configuration from scratch:
从头搭建新的安全 `.claude/` 配置：

```bash
npx ecc-agentshield init
```

```bash
npx ecc-agentshield init
```

Creates:
创建：

- `settings.json` with scoped permissions and deny list
- 具有作用域权限和拒绝列表的 `settings.json`
- `CLAUDE.md` with security best practices
- 具有安全最佳实践的 `CLAUDE.md`
- `mcp.json` placeholder
- `mcp.json` 占位符

### GitHub Action
### GitHub Action

Add to your CI pipeline:
添加到您的 CI 管道：

```yaml
- uses: affaan-m/agentshield@v1
  with:
    path: '.'
    min-severity: 'medium'
    fail-on-findings: true
```

```yaml
- uses: affaan-m/agentshield@v1
  with:
    path: '.'
    min-severity: 'medium'
    fail-on-findings: true
```

## Severity Levels
## 严重级别

| Grade | Score | Meaning |
|-------|-------|---------|
| A | 90-100 | Secure configuration |
| B | 75-89 | Minor issues |
| C | 60-74 | Needs attention |
| D | 40-59 | Significant risks |
| F | 0-39 | Critical vulnerabilities |

| 等级 | 分数 | 含义 |
|-------|-------|---------|
| A | 90-100 | 安全配置 |
| B | 75-89 | 轻微问题 |
| C | 60-74 | 需要注意 |
| D | 40-59 | 重大风险 |
| F | 0-39 | 严重漏洞 |

## Interpreting Results
## 解读结果

### Critical Findings (fix immediately)
### 严重发现（立即修复）

- Hardcoded API keys or tokens in config files
- 配置文件中硬编码的 API 密钥或令牌
- `Bash(*)` in the allow list (unrestricted shell access)
- 允许列表中的 `Bash(*)`（无限制的 shell 访问）
- Command injection in hooks via `${file}` interpolation
- 通过 `${file}` 插值在 hooks 中注入命令
- Shell-running MCP servers
- 运行 shell 的 MCP 服务器

### High Findings (fix before production)
### 高风险发现（生产前修复）

- Auto-run instructions in CLAUDE.md (prompt injection vector)
- CLAUDE.md 中的自动运行指令（提示注入向量）
- Missing deny lists in permissions
- 权限中缺失拒绝列表
- Agents with unnecessary Bash access
- 具有不必要 Bash 访问权限的 Agent

### Medium Findings (recommended)
### 中等发现（建议修复）

- Silent error suppression in hooks (`2>/dev/null`, `|| true`)
- hooks 中的静默错误抑制（`2>/dev/null`、`|| true`）
- Missing PreToolUse security hooks
- 缺失的 PreToolUse 安全 hooks
- `npx -y` auto-install in MCP server configs
- MCP 服务器配置中的 `npx -y` 自动安装

### Info Findings (awareness)
### 信息发现（关注）

- Missing descriptions on MCP servers
- MCP 服务器上缺少描述
- Prohibitive instructions correctly flagged as good practice
- 正确标记为良好实践的禁止性指令

## Links
## 链接

- **GitHub**: [github.com/affaan-m/agentshield](https://github.com/affaan-m/agentshield)
- **npm**: [npmjs.com/package/ecc-agentshield](https://www.npmjs.com/package/ecc-agentshield)
