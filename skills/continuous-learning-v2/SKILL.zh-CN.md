---
name: continuous-learning-v2
description: Instinct-based learning system that observes sessions via hooks, creates atomic instincts with confidence scoring, and evolves them into skills/commands/agents. v2.1 adds project-scoped instincts to prevent cross-project contamination.
description zh-CN: 基于本能的学习系统，通过 hooks 观察会话，创建带有置信度评分的原子化本能，并将它们进化为技能/命令/agent。v2.1 添加了项目范围的本能以防止跨项目污染。
origin: ECC
version: 2.1.0
---

# Continuous Learning v2.1 - Instinct-Based Architecture
# Continuous Learning v2.1 - Instinct-Based Architecture
# 持续学习 v2.1 - 基于本能的架构

An advanced learning system that turns your Claude Code sessions into reusable knowledge through atomic "instincts" - small learned behaviors with confidence scoring.
## An advanced learning system that turns your Claude Code sessions into reusable knowledge through atomic "instincts" - small learned behaviors with confidence scoring.
## 一个先进的学习系统，通过原子化的"本能"——带有置信度评分的小型学习行为——将 Claude Code 会话转化为可重用的知识。

**v2.1** adds **project-scoped instincts** — React patterns stay in your React project, Python conventions stay in your Python project, and universal patterns (like "always validate input") are shared globally.
## **v2.1** adds **project-scoped instincts** — React patterns stay in your React project, Python conventions stay in your Python project, and universal patterns (like "always validate input") are shared globally.
## **v2.1** 添加了**项目范围的本能**——React 模式保留在你的 React 项目中，Python 约定保留在你的 Python 项目中，而通用模式（如"始终验证输入"）则全局共享。

## When to Activate
## When to Activate
## 何时激活

- Setting up automatic learning from Claude Code sessions
  - 从 Claude Code 会话设置自动学习
- Configuring instinct-based behavior extraction via hooks
  - 通过 hooks 配置基于本能的行为提取
- Tuning confidence thresholds for learned behaviors
  - 调整学习行为的置信度阈值
- Reviewing, exporting, or importing instinct libraries
  - 查看、导出或导入本能库
- Evolving instincts into full skills, commands, or agents
  - 将本能进化为完整技能、命令或 agent
- Managing project-scoped vs global instincts
  - 管理项目范围与全局本能
- Promoting instincts from project to global scope
  - 将本能从项目提升到全局范围

## What's New in v2.1
## What's New in v2.1
## v2.1 新特性

| Feature | v2.0 | v2.1 |
|---------|------|------|
| Storage | Global (~/.claude/homunculus/) | Project-scoped (projects/\<hash\>/) |
| 存储位置 | 全局（~/.claude/homunculus/） | 项目范围（projects/\<hash\>/） |
| Scope | All instincts apply everywhere | Project-scoped + global |
| 范围 | 所有本能全局适用 | 项目范围 + 全局 |
| Detection | None | git remote URL / repo path |
| 检测方式 | 无 | git remote URL / 仓库路径 |
| Promotion | N/A | Project -> global when seen in 2+ projects |
| 提升机制 | 不适用 | 在 2+ 项目中出现时从项目提升到全局 |
| Commands | 4 (status/evolve/export/import) | 6 (+promote/projects) |
| 命令 | 4 个（status/evolve/export/import） | 6 个（+promote/projects） |
| Cross-project | Contamination risk | Isolated by default |
| 跨项目 | 污染风险 | 默认隔离 |

## What's New in v2 (vs v1)
## What's New in v2 (vs v1)
## v2 新特性（对比 v1）

| Feature | v1 | v2 |
|---------|----|----|
| Observation | Stop hook (session end) | PreToolUse/PostToolUse (100% reliable) |
| 观察方式 | Stop hook（会话结束时） | PreToolUse/PostToolUse（100% 可靠） |
| Analysis | Main context | Background agent (Haiku) |
| 分析方式 | 主上下文 | 后台 agent（Haiku） |
| Granularity | Full skills | Atomic "instincts" |
| 粒度 | 完整技能 | 原子化"本能" |
| Confidence | None | 0.3-0.9 weighted |
| 置信度 | 无 | 0.3-0.9 加权 |
| Evolution | Direct to skill | Instincts -> cluster -> skill/command/agent |
| 进化方式 | 直接转为技能 | 本能 -> 聚类 -> 技能/命令/agent |
| Sharing | None | Export/import instincts |
| 共享方式 | 无 | 导出/导入本能 |

## The Instinct Model
## The Instinct Model
## 本能模型

An instinct is a small learned behavior:
## An instinct is a small learned behavior.
## 本能是一个小型学习行为：

```yaml
---
id: prefer-functional-style
trigger: "when writing new functions"
confidence: 0.7
domain: "code-style"
source: "session-observation"
scope: project
project_id: "a1b2c3d4e5f6"
project_name: "my-react-app"
---

# Prefer Functional Style

## Action
Use functional patterns over classes when appropriate.

## Evidence
- Observed 5 instances of functional pattern preference
- User corrected class-based approach to functional on 2025-01-15
```

**Properties:**
## **Properties:**
## **属性：**

- **Atomic** -- one trigger, one action
  - **原子化** -- 一个触发器，一个动作
- **Confidence-weighted** -- 0.3 = tentative, 0.9 = near certain
  - **置信度加权** -- 0.3 = 试探性，0.9 = 几乎确定
- **Domain-tagged** -- code-style, testing, git, debugging, workflow, etc.
  - **领域标签** -- code-style、testing、git、debugging、workflow 等
- **Evidence-backed** -- tracks what observations created it
  - **证据支持** -- 追踪创建它的观察记录
- **Scope-aware** -- `project` (default) or `global`
  - **范围感知** -- `project`（默认）或 `global`

## How It Works
## How It Works
## 工作原理

```
Session Activity (in a git repo)
      |
      | Hooks capture prompts + tool use (100% reliable)
      | + detect project context (git remote / repo path)
      v
+---------------------------------------------+
|  projects/<project-hash>/observations.jsonl  |
|   (prompts, tool calls, outcomes, project)   |
+---------------------------------------------+
      |
      | Observer agent reads (background, Haiku)
      v
+---------------------------------------------+
|          PATTERN DETECTION                   |
|   * User corrections -> instinct             |
|   * Error resolutions -> instinct            |
|   * Repeated workflows -> instinct           |
|   * Scope decision: project or global?       |
+---------------------------------------------+
      |
      | Creates/updates
      v
+---------------------------------------------+
|  projects/<project-hash>/instincts/personal/ |
|   * prefer-functional.yaml (0.7) [project]   |
|   * use-react-hooks.yaml (0.9) [project]     |
+---------------------------------------------+
|  instincts/personal/  (GLOBAL)               |
|   * always-validate-input.yaml (0.85) [global]|
|   * grep-before-edit.yaml (0.6) [global]     |
+---------------------------------------------+
      |
      | /evolve clusters + /promote
      v
+---------------------------------------------+
|  projects/<hash>/evolved/ (project-scoped)   |
|  evolved/ (global)                           |
|   * commands/new-feature.md                  |
|   * skills/testing-workflow.md               |
|   * agents/refactor-specialist.md            |
+---------------------------------------------+
```

## Project Detection
## Project Detection
## 项目检测

The system automatically detects your current project:
## The system automatically detects your current project.
## 系统自动检测当前项目：

1. **`CLAUDE_PROJECT_DIR` env var** (highest priority)
   - **`CLAUDE_PROJECT_DIR` 环境变量**（最高优先级）
2. **`git remote get-url origin`** -- hashed to create a portable project ID (same repo on different machines gets the same ID)
   - **`git remote get-url origin`** -- 哈希生成可移植的项目 ID（同一仓库在不同机器上获得相同的 ID）
3. **`git rev-parse --show-toplevel`** -- fallback using repo path (machine-specific)
   - **`git rev-parse --show-toplevel`** -- 使用仓库路径作为备选（机器特定）
4. **Global fallback** -- if no project is detected, instincts go to global scope
   - **全局降级** -- 如果未检测到项目，本能进入全局范围

Each project gets a 12-character hash ID (e.g., `a1b2c3d4e5f6`). A registry file at `~/.claude/homunculus/projects.json` maps IDs to human-readable names.
## Each project gets a 12-character hash ID (e.g., `a1b2c3d4e5f6`). A registry file at `~/.claude/homunculus/projects.json` maps IDs to human-readable names.
## 每个项目获得一个 12 字符的哈希 ID（例如 `a1b2c3d4e5f6`）。注册表文件位于 `~/.claude/homunculus/projects.json`，将 ID 映射到人类可读的名称。

## Quick Start
## Quick Start
## 快速开始

### 1. Enable Observation Hooks
### 1. Enable Observation Hooks
### 1. 启用观察 Hooks

Add to your `~/.claude/settings.json`.
## Add to your `~/.claude/settings.json`.
## 添加到你的 `~/.claude/settings.json`。

**If installed as a plugin** (recommended):
## **If installed as a plugin** (recommended).
## **如果作为插件安装**（推荐）：

```json
{
  "hooks": {
    "PreToolUse": [{
      "matcher": "*",
      "hooks": [{
        "type": "command",
        "command": "${CLAUDE_PLUGIN_ROOT}/skills/continuous-learning-v2/hooks/observe.sh"
      }]
    }],
    "PostToolUse": [{
      "matcher": "*",
      "hooks": [{
        "type": "command",
        "command": "${CLAUDE_PLUGIN_ROOT}/skills/continuous-learning-v2/hooks/observe.sh"
      }]
    }]
  }
}
```

**If installed manually** to `~/.claude/skills`:
## **If installed manually** to `~/.claude/skills`.
## **如果手动安装**到 `~/.claude/skills`：

```json
{
  "hooks": {
    "PreToolUse": [{
      "matcher": "*",
      "hooks": [{
        "type": "command",
        "command": "~/.claude/skills/continuous-learning-v2/hooks/observe.sh"
      }]
    }],
    "PostToolUse": [{
      "matcher": "*",
      "hooks": [{
        "type": "command",
        "command": "~/.claude/skills/continuous-learning-v2/hooks/observe.sh"
      }]
    }]
  }
}
```

### 2. Initialize Directory Structure
### 2. Initialize Directory Structure
### 2. 初始化目录结构

The system creates directories automatically on first use, but you can also create them manually:
## The system creates directories automatically on first use, but you can also create them manually.
## 系统在首次使用时自动创建目录，但你也可以手动创建：

```bash
# Global directories
# 全局目录
mkdir -p ~/.claude/homunculus/{instincts/{personal,inherited},evolved/{agents,skills,commands},projects}

# Project directories are auto-created when the hook first runs in a git repo
# 项目目录在 hook 首次在 git 仓库中运行时自动创建
```

### 3. Use the Instinct Commands
### 3. Use the Instinct Commands
### 3. 使用本能命令

```bash
/instinct-status     # Show learned instincts (project + global)
  # 显示已学本能（项目 + 全局）
/evolve              # Cluster related instincts into skills/commands
  # 将相关本能聚类为技能/命令
/instinct-export     # Export instincts to file
  # 导出本能到文件
/instinct-import     # Import instincts from others
  # 从他人导入本能
/promote             # Promote project instincts to global scope
  # 将项目本能提升到全局范围
/projects            # List all known projects and their instinct counts
  # 列出所有已知项目及其本能数量
```

## Commands
## Commands
## 命令

| Command | Description |
|---------|-------------|
| `/instinct-status` | Show all instincts (project-scoped + global) with confidence |
| `/instinct-status` | 显示所有本能（项目范围 + 全局）及置信度 |
| `/evolve` | Cluster related instincts into skills/commands, suggest promotions |
| `/evolve` | 将相关本能聚类为技能/命令，建议提升 |
| `/instinct-export` | Export instincts (filterable by scope/domain) |
| `/instinct-export` | 导出本能（可按范围/领域过滤） |
| `/instinct-import <file>` | Import instincts with scope control |
| `/instinct-import <file>` | 带范围控制导入本能 |
| `/promote [id]` | Promote project instincts to global scope |
| `/promote [id]` | 将项目本能提升到全局范围 |
| `/projects` | List all known projects and their instinct counts |
| `/projects` | 列出所有已知项目及其本能数量 |

## Configuration
## Configuration
## 配置

Edit `config.json` to control the background observer:
## Edit `config.json` to control the background observer.
## 编辑 `config.json` 以控制后台观察器：

```json
{
  "version": "2.1",
  "observer": {
    "enabled": false,
    "run_interval_minutes": 5,
    "min_observations_to_analyze": 20
  }
}
```

| Key | Default | Description |
|-----|---------|-------------|
| `observer.enabled` | `false` | Enable the background observer agent |
| `observer.enabled` | `false` | 启用后台观察器 agent |
| `observer.run_interval_minutes` | `5` | How often the observer analyzes observations |
| `observer.run_interval_minutes` | `5` | 观察器分析观察结果的频率 |
| `observer.min_observations_to_analyze` | `20` | Minimum observations before analysis runs |
| `observer.min_observations_to_analyze` | `20` | 分析运行前的最小观察数 |

Other behavior (observation capture, instinct thresholds, project scoping, promotion criteria) is configured via code defaults in `instinct-cli.py` and `observe.sh`.
## Other behavior (observation capture, instinct thresholds, project scoping, promotion criteria) is configured via code defaults in `instinct-cli.py` and `observe.sh`.
## 其他行为（观察捕获、本能阈值、项目范围、提升标准）通过 `instinct-cli.py` 和 `observe.sh` 中的代码默认值进行配置。

## File Structure
## File Structure
## 文件结构

```
~/.claude/homunculus/
+-- identity.json           # Your profile, technical level
  # 你的个人资料、技术水平
+-- projects.json           # Registry: project hash -> name/path/remote
  # 注册表：项目哈希 -> 名称/路径/远程
+-- observations.jsonl      # Global observations (fallback)
  # 全局观察（降级）
+-- instincts/
|   +-- personal/           # Global auto-learned instincts
  # 全局自动学习的本能
|   +-- inherited/          # Global imported instincts
  # 全局导入的本能
+-- evolved/
|   +-- agents/             # Global generated agents
  # 全局生成的 agents
|   +-- skills/             # Global generated skills
  # 全局生成的技能
|   +-- commands/           # Global generated commands
  # 全局生成的命令
+-- projects/
    +-- a1b2c3d4e5f6/       # Project hash (from git remote URL)
  # 项目哈希（来自 git remote URL）
    |   +-- project.json    # Per-project metadata mirror (id/name/root/remote)
  # 每个项目的元数据镜像（id/name/root/remote）
    |   +-- observations.jsonl
    |   +-- observations.archive/
    |   +-- instincts/
    |   |   +-- personal/   # Project-specific auto-learned
  # 项目特定的自动学习
    |   |   +-- inherited/  # Project-specific imported
  # 项目特定的导入
    |   +-- evolved/
    |       +-- skills/
    |       +-- commands/
    |       +-- agents/
    +-- f6e5d4c3b2a1/       # Another project
  # 另一个项目
        +-- ...
```

## Scope Decision Guide
## Scope Decision Guide
## 范围决策指南

| Pattern Type | Scope | Examples |
|-------------|-------|---------|
| Language/framework conventions | **project** | "Use React hooks", "Follow Django REST patterns" |
| 语言/框架约定 | **project** | "Use React hooks", "Follow Django REST patterns" |
| File structure preferences | **project** | "Tests in `__tests__`/", "Components in src/components/" |
| 文件结构偏好 | **project** | "Tests in `__tests__`/", "Components in src/components/" |
| Code style | **project** | "Use functional style", "Prefer dataclasses" |
| 代码风格 | **project** | "Use functional style", "Prefer dataclasses" |
| Error handling strategies | **project** | "Use Result type for errors" |
| 错误处理策略 | **project** | "Use Result type for errors" |
| Security practices | **global** | "Validate user input", "Sanitize SQL" |
| 安全实践 | **global** | "Validate user input", "Sanitize SQL" |
| General best practices | **global** | "Write tests first", "Always handle errors" |
| 通用最佳实践 | **global** | "Write tests first", "Always handle errors" |
| Tool workflow preferences | **global** | "Grep before Edit", "Read before Write" |
| 工具工作流偏好 | **global** | "Grep before Edit", "Read before Write" |
| Git practices | **global** | "Conventional commits", "Small focused commits" |
| Git 实践 | **global** | "Conventional commits", "Small focused commits" |

## Instinct Promotion (Project -> Global)
## Instinct Promotion (Project -> Global)
## 本能提升（项目 -> 全局）

When the same instinct appears in multiple projects with high confidence, it's a candidate for promotion to global scope.
## When the same instinct appears in multiple projects with high confidence, it's a candidate for promotion to global scope.
## 当相同的本能在多个项目中以高置信度出现时，它是提升到全局范围的候选者。

**Auto-promotion criteria:**
## **Auto-promotion criteria:**
## **自动提升标准：**

- Same instinct ID in 2+ projects
  - 相同的本能 ID 出现在 2+ 个项目中
- Average confidence >= 0.8
  - 平均置信度 >= 0.8

**How to promote:**
## **How to promote:**
## **如何提升：**

```bash
# Promote a specific instinct
# 提升特定本能
python3 instinct-cli.py promote prefer-explicit-errors

# Auto-promote all qualifying instincts
# 自动提升所有符合条件的本能
python3 instinct-cli.py promote

# Preview without changes
# 预览但不进行更改
python3 instinct-cli.py promote --dry-run
```

The `/evolve` command also suggests promotion candidates.
## The `/evolve` command also suggests promotion candidates.
## `/evolve` 命令也会建议提升候选者。

## Confidence Scoring
## Confidence Scoring
## 置信度评分

Confidence evolves over time:
## Confidence evolves over time.
## 置信度随时间演变：

| Score | Meaning | Behavior |
|-------|---------|----------|
| 0.3 | Tentative | Suggested but not enforced |
| 0.3 | 试探性 | 建议但不强制 |
| 0.5 | Moderate | Applied when relevant |
| 0.5 | 中等 | 在相关时应用 |
| 0.7 | Strong | Auto-approved for application |
| 0.7 | 强 | 自动批准应用 |
| 0.9 | Near-certain | Core behavior |
| 0.9 | 几乎确定 | 核心行为 |

**Confidence increases** when:
## **Confidence increases** when:
## **置信度增加**当：

- Pattern is repeatedly observed
  - 模式被反复观察
- User doesn't correct the suggested behavior
  - 用户没有纠正建议的行为
- Similar instincts from other sources agree
  - 其他来源的相似本能一致

**Confidence decreases** when:
## **Confidence decreases** when:
## **置信度降低**当：

- User explicitly corrects the behavior
  - 用户明确纠正该行为
- Pattern isn't observed for extended periods
  - 模式在延长期间未被观察
- Contradicting evidence appears
  - 出现矛盾的证据

## Why Hooks vs Skills for Observation?
## Why Hooks vs Skills for Observation?
## 为什么使用 Hooks 而不是 Skills 进行观察？

> "v1 relied on skills to observe. Skills are probabilistic -- they fire ~50-80% of the time based on Claude's judgment."
> "v1 依赖技能来观察。技能是概率性的——基于 Claude 的判断大约 50-80% 的时间会触发。"

Hooks fire **100% of the time**, deterministically. This means:
## Hooks fire **100% of the time**, deterministically. This means:
## Hooks **100% 触发**，确定性。这意味着：

- Every tool call is observed
  - 每个工具调用都被观察
- No patterns are missed
  - 没有模式被遗漏
- Learning is comprehensive
  - 学习是全面的

## Backward Compatibility
## Backward Compatibility
## 向后兼容性

v2.1 is fully compatible with v2.0 and v1:
## v2.1 is fully compatible with v2.0 and v1.
## v2.1 与 v2.0 和 v1 完全兼容：

- Existing global instincts in `~/.claude/homunculus/instincts/` still work as global instincts
  - `~/.claude/homunculus/instincts/` 中现有的全局本能仍然作为全局本能工作
- Existing `~/.claude/skills/learned/` skills from v1 still work
  - v1 中现有的 `~/.claude/skills/learned/` 技能仍然工作
- Stop hook still runs (but now also feeds into v2)
  - Stop hook 仍然运行（但现在也输入到 v2）
- Gradual migration: run both in parallel
  - 渐进迁移：并行运行两者

## Privacy
## Privacy
## 隐私

- Observations stay **local** on your machine
  - 观察结果保留在本地机器上
- Project-scoped instincts are isolated per project
  - 项目范围的本能按项目隔离
- Only **instincts** (patterns) can be exported — not raw observations
  - 只能导出**本能**（模式）——不能导出原始观察
- No actual code or conversation content is shared
  - 不共享实际代码或对话内容
- You control what gets exported and promoted
  - 你控制导出和提升的内容

## Related
## Related
## 相关内容

- [Skill Creator](https://skill-creator.app) - Generate instincts from repo history
  - [Skill Creator](https://skill-creator.app) - 从仓库历史生成本能
- Homunculus - Community project that inspired the v2 instinct-based architecture (atomic observations, confidence scoring, instinct evolution pipeline)
  - Homunculus - 社区项目，启发了 v2 基于本能的架构（原子观察、置信度评分、本能进化管道）
- [The Longform Guide](https://x.com/affaanmustafa/status/2014040193557471352) - Continuous learning section
  - [The Longform Guide](https://x.com/affaanmustafa/status/2014040193557471352) - 持续学习部分

---

*Instinct-based learning: teaching Claude your patterns, one project at a time.*
## *Instinct-based learning: teaching Claude your patterns, one project at a time.*
## *基于本能的学习：一次一个项目，教 Claude 你的模式。*
