---
name: team-builder
description: Interactive agent picker for composing and dispatching parallel teams
description zh-CN: 用于组合和派发并行团队的交互式代理选择器
origin: community
---

# Team Builder

## Team Builder
## 团队构建器

Interactive menu for browsing and composing agent teams on demand. Works with flat or domain-subdirectory agent collections.

用于按需浏览和组合代理团队的交互式菜单。支持扁平或领域子目录的代理集合。

## When to Use

## When to Use
## 何时使用

- You have multiple agent personas (markdown files) and want to pick which ones to use for a task
  - 你有多个代理角色（markdown 文件）并想选择使用哪些来处理任务
- You want to compose an ad-hoc team from different domains (e.g., Security + SEO + Architecture)
  - 你想从不同领域组成临时团队（如安全 + SEO + 架构）
- You want to browse what agents are available before deciding
  - 你想在决定前浏览可用的代理

## Prerequisites

## Prerequisites
## 前提条件

Agent files must be markdown files containing a persona prompt (identity, rules, workflow, deliverables). The first `# Heading` is used as the agent name and the first paragraph as the description.

代理文件必须是包含角色提示的 markdown 文件（身份、规则、工作流、可交付成果）。第一个 `# 标题` 用作代理名称，第一段作为描述。

Both flat and subdirectory layouts are supported:

支持扁平布局和子目录布局：

**Subdirectory layout** — domain is inferred from the folder name:

**子目录布局** — 领域从文件夹名称推断：

```
agents/
├── engineering/
│   ├── security-engineer.md
│   └── software-architect.md
├── marketing/
│   └── seo-specialist.md
└── sales/
    └── discovery-coach.md
```

**Flat layout** — domain inferred from shared filename prefixes. A prefix counts as a domain when 2+ files share it. Files with unique prefixes go to "General". Note: the algorithm splits at the first `-`, so multi-word domains (e.g., `product-management`) should use the subdirectory layout instead:

**扁平布局** — 领域从共享的文件名前缀推断。当 2 个或更多文件共享前缀时，该前缀计为一个领域。具有唯一前缀的文件归入"通用"。注意：算法在第一个 `-` 处分割，因此多词领域（如 `product-management`）应改用子目录布局：

```
agents/
├── engineering-security-engineer.md
├── engineering-software-architect.md
├── marketing-seo-specialist.md
├── marketing-content-strategist.md
├── sales-discovery-coach.md
└── sales-outbound-strategist.md
```

## Configuration

## Configuration
## 配置

Agent directories are probed in order and results are merged:

按顺序探测代理目录并合并结果：

1. `./agents/**/*.md` + `./agents/*.md` — project-local agents (both depths)
   - 项目本地的代理（两种深度）
2. `~/.claude/agents/**/*.md` + `~/.claude/agents/*.md` — global agents (both depths)
   - 全局代理（两种深度）

Results from all locations are merged and deduplicated by agent name. Project-local agents take precedence over global agents with the same name. A custom path can be used instead if the user specifies one.

来自所有位置的结果按代理名称合并和去重。项目本地的代理优先于具有相同名称的全局代理。如果用户指定了自定义路径，则使用该路径。

## How It Works

## How It Works
## 工作原理

### Step 1: Discover Available Agents

### Step 1: Discover Available Agents
### 步骤 1：发现可用代理

Glob agent directories using the probe order above. Exclude README files. For each file found:
- **Subdirectory layout:** extract the domain from the parent folder name
- **Flat layout:** collect all filename prefixes (text before the first `-`). A prefix qualifies as a domain only if it appears in 2 or more filenames (e.g., `engineering-security-engineer.md` and `engineering-software-architect.md` both start with `engineering` → Engineering domain). Files with unique prefixes (e.g., `code-reviewer.md`, `tdd-guide.md`) are grouped under "General"
- Extract the agent name from the first `# Heading`. If no heading is found, derive the name from the filename (strip `.md`, replace hyphens with spaces, title-case)
- Extract a one-line summary from the first paragraph after the heading

使用上述探测顺序 glob 代理目录。排除 README 文件。对于找到的每个文件：
- **子目录布局：** 从父文件夹名称提取领域
- **扁平布局：** 收集所有文件名前缀（第一个 `-` 之前的文本）。前缀仅在出现在 2 个或更多文件名中时才计为一个领域（如 `engineering-security-engineer.md` 和 `engineering-software-architect.md` 都以 `engineering` 开头 → Engineering 领域）。具有唯一前缀的文件（如 `code-reviewer.md`、`tdd-guide.md`）归入"通用"
- 从第一个 `# 标题` 提取代理名称。如果找不到标题，从文件名派生名称（去掉 `.md`，将连字符替换为空格，标题大小写）
- 从标题后的第一段提取一行摘要

If no agent files are found after probing all locations, inform the user: "No agent files found. Checked: [list paths probed]. Expected: markdown files in one of those directories." Then stop.

如果在探测所有位置后未找到代理文件，通知用户："未找到代理文件。已检查：[探测的路径列表]。预期：在这些目录之一的 markdown 文件。"然后停止。

### Step 2: Present Domain Menu

### Step 2: Present Domain Menu
### 步骤 2：展示领域菜单

```
Available agent domains:
1. Engineering — Software Architect, Security Engineer
2. Marketing — SEO Specialist
3. Sales — Discovery Coach, Outbound Strategist

Pick domains or name specific agents (e.g., "1,3" or "security + seo"):
```

- Skip domains with zero agents (empty directories)
  - 跳过没有代理的领域（空目录）
- Show agent count per domain
  - 显示每个领域的代理数量

### Step 3: Handle Selection

### Step 3: Handle Selection
### 步骤 3：处理选择

Accept flexible input:
接受灵活输入：
- Numbers: "1,3" selects all agents from Engineering and Sales
  - 数字："1,3" 选择 Engineering 和 Sales 的所有代理
- Names: "security + seo" fuzzy-matches against discovered agents
  - 名称："security + seo" 模糊匹配已发现的代理
- "all from engineering" selects every agent in that domain
  - "all from engineering" 选择该领域的所有代理

If more than 5 agents are selected, list them alphabetically and ask the user to narrow down: "You selected N agents (max 5). Pick which to keep, or say 'first 5' to use the first five alphabetically."

如果选择了超过 5 个代理，按字母顺序列出并要求用户缩小范围："你选择了 N 个代理（最多 5 个）。选择保留哪些，或说 'first 5' 使用前 5 个按字母顺序。"

Confirm selection:
确认选择：
```
Selected: Security Engineer + SEO Specialist
What should they work on? (describe the task):
```

### Step 4: Spawn Agents in Parallel

### Step 4: Spawn Agents in Parallel
### 步骤 4：并行生成代理

1. Read each selected agent's markdown file
   - 读取每个选定代理的 markdown 文件
2. Prompt for the task description if not already provided
   - 如果尚未提供，提示任务描述
3. Spawn all agents in parallel using the Agent tool:
   - 使用 Agent 工具并行派发所有代理：
   - `subagent_type: "general-purpose"`
   - `prompt: "{agent file content}\n\nTask: {task description}"`
   - Each agent runs independently — no inter-agent communication needed
   - 每个代理独立运行 — 不需要代理间通信
4. If an agent fails (error, timeout, or empty output), note the failure inline (e.g., "Security Engineer: failed — [reason]") and continue with results from agents that succeeded
   - 如果代理失败（错误、超时或空输出），内联记录失败（如 "Security Engineer: failed — [reason]"）并继续使用成功代理的结果

### Step 5: Synthesize Results

### Step 5: Synthesize Results
### 步骤 5：综合结果

Collect all outputs and present a unified report:
收集所有输出并呈现统一报告：
- Results grouped by agent
  - 按代理分组的结果
- Synthesis section highlighting:
  - 综合部分突出显示：
  - Agreements across agents
    - 代理间的共识
  - Conflicts or tensions between recommendations
    - 建议之间的冲突或张力
  - Recommended next steps
    - 建议的后续步骤

If only 1 agent was selected, skip synthesis and present the output directly.

如果只选择了 1 个代理，跳过综合直接呈现输出。

## Rules

## Rules
## 规则

- **Dynamic discovery only.** Never hardcode agent lists. New files in the directory auto-appear in the menu.
  - **仅动态发现。** 永不硬编码代理列表。目录中的新文件会自动出现在菜单中。
- **Max 5 agents per team.** More than 5 produces diminishing returns and excessive token usage. Enforce at selection time.
  - **每个团队最多 5 个代理。** 超过 5 个会产生递减回报和过多 token 使用。在选择时强制执行。
- **Parallel dispatch.** All agents run simultaneously — use the Agent tool's parallel invocation pattern.
  - **并行派发。** 所有代理同时运行 — 使用 Agent 工具的并行调用模式。
- **Parallel Agent calls, not TeamCreate.** This skill uses parallel Agent tool calls for independent work. TeamCreate (a Claude Code tool for multi-agent dialogue) is only needed when agents must debate or respond to each other.
  - **并行 Agent 调用，而非 TeamCreate。** 此 skill 使用并行 Agent 工具调用进行独立工作。TeamCreate（Claude Code 的多代理对话工具）仅在代理必须辩论或相互响应时需要。

## Examples

## Examples
## 示例

```
User: team builder

Claude:
Available agent domains:
1. Engineering (2) — Software Architect, Security Engineer
2. Marketing (1) — SEO Specialist
3. Sales (4) — Discovery Coach, Outbound Strategist, Proposal Strategist, Sales Engineer
4. Support (1) — Executive Summary

Pick domains or name specific agents:

User: security + seo

Claude:
Selected: Security Engineer + SEO Specialist
What should they work on?

User: Review my Next.js e-commerce site before launch

[Both agents spawn in parallel, each applying their specialty to the codebase]

Claude:
## Security Engineer Findings
- [findings...]

## SEO Specialist Findings
- [findings...]

## Synthesis
Both agents agree on: [...]
Tension: Security recommends CSP that blocks inline styles, SEO needs inline schema markup. Resolution: [...]
Next steps: [...]
```
