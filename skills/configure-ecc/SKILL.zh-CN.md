---
name: configure-ecc
description: Interactive installer for Everything Claude Code — guides users through selecting and installing skills and rules to user-level or project-level directories, verifies paths, and optionally optimizes installed files.
description zh-CN: Everything Claude Code 的交互式安装程序——引导用户选择并安装技能和规则到用户级或项目级目录，验证路径，并可选地优化已安装的文件。
origin: ECC
---

# Configure Everything Claude Code (ECC)
# 配置 Everything Claude Code (ECC)

An interactive, step-by-step installation wizard for the Everything Claude Code project. Uses `AskUserQuestion` to guide users through selective installation of skills and rules, then verifies correctness and offers optimization.
Everything Claude Code 项目的交互式分步安装向导。使用 `AskUserQuestion` 引导用户选择性安装技能和规则，然后验证正确性并提供优化选项。

## When to Activate
## 何时激活

- User says "configure ecc", "install ecc", "setup everything claude code", or similar
  - 用户说"配置 ecc"、"安装 ecc"、"设置 everything claude code"或类似
- User wants to selectively install skills or rules from this project
  - 用户想从此项目选择性安装技能或规则
- User wants to verify or fix an existing ECC installation
  - 用户想验证或修复现有 ECC 安装
- User wants to optimize installed skills or rules for their project
  - 用户想为项目优化已安装的技能或规则

## Prerequisites
## 前置条件

This skill must be accessible to Claude Code before activation. Two ways to bootstrap:
此技能必须在激活前对 Claude Code 可用。有两种引导方式：
1. **Via Plugin**: `/plugin install everything-claude-code` — the plugin loads this skill automatically
   - **通过插件**：`/plugin install everything-claude-code` —— 插件自动加载此技能
2. **Manual**: Copy only this skill to `~/.claude/skills/configure-ecc/SKILL.md`, then activate by saying "configure ecc"
   - **手动**：仅将此技能复制到 `~/.claude/skills/configure-ecc/SKILL.md`，然后说"configure ecc"激活

---

## Step 0: Clone ECC Repository
## 步骤 0：克隆 ECC 仓库

Before any installation, clone the latest ECC source to `/tmp`:
在任何安装之前，将最新 ECC 源码克隆到 `/tmp`：

```bash
rm -rf /tmp/everything-claude-code
git clone https://github.com/affaan-m/everything-claude-code.git /tmp/everything-claude-code
```

Set `ECC_ROOT=/tmp/everything-claude-code` as the source for all subsequent copy operations.
将 `ECC_ROOT=/tmp/everything-claude-code` 作为所有后续复制操作的源。

If the clone fails (network issues, etc.), use `AskUserQuestion` to ask the user to provide a local path to an existing ECC clone.
如果克隆失败（网络问题等），使用 `AskUserQuestion` 询问用户提供现有 ECC 克隆的本地路径。

---

## Step 1: Choose Installation Level
## 步骤 1：选择安装级别

Use `AskUserQuestion` to ask the user where to install:
使用 `AskUserQuestion` 询问用户安装位置：

```
Question: "Where should ECC components be installed?"
Options:
  - "User-level (~/.claude/)" — "Applies to all your Claude Code projects"
  - "Project-level (.claude/)" — "Applies only to the current project"
  - "Both" — "Common/shared items user-level, project-specific items project-level"
```

问题：ECC 组件应该安装在哪里？
选项：
  - "用户级（~/.claude/）" — "适用于您所有 Claude Code 项目"
  - "项目级（.claude/）" — "仅适用于当前项目"
  - "两者" — "通用/共享项用户级，项目特定项项目级"

Store the choice as `INSTALL_LEVEL`. Set the target directory:
将选择存储为 `INSTALL_LEVEL`。设置目标目录：
- User-level: `TARGET=~/.claude`
- 用户级：`TARGET=~/.claude`
- Project-level: `TARGET=.claude` (relative to current project root)
- 项目级：`TARGET=.claude`（相对于当前项目根目录）
- Both: `TARGET_USER=~/.claude`, `TARGET_PROJECT=.claude`
- 两者：`TARGET_USER=~/.claude`、`TARGET_PROJECT=.claude`

Create the target directories if they don't exist:
如果目标目录不存在则创建：
```bash
mkdir -p $TARGET/skills $TARGET/rules
```

---

## Step 2: Select & Install Skills
## 步骤 2：选择并安装技能

### 2a: Choose Scope (Core vs Niche)
### 2a：选择范围（核心 vs 利基）

Default to **Core (recommended for new users)** — copy `.agents/skills/*` plus `skills/search-first/` for research-first workflows. This bundle covers engineering, evals, verification, security, strategic compaction, frontend design, and Anthropic cross-functional skills (article-writing, content-engine, market-research, frontend-slides).
默认选择**核心（推荐新用户）**——复制 `.agents/skills/*` 加 `skills/search-first/` 用于研究优先工作流。此捆绑包涵盖工程、评估、验证、安全、战略压缩、前端设计及 Anthropic 跨职能技能（文章写作、内容引擎、市场研究、前端幻灯片）。

Use `AskUserQuestion` (single select):
使用 `AskUserQuestion`（单选）：
```
Question: "Install core skills only, or include niche/framework packs?"
Options:
  - "Core only (recommended)" — "tdd, e2e, evals, verification, research-first, security, frontend patterns, compacting, cross-functional Anthropic skills"
  - "Core + selected niche" — "Add framework/domain-specific skills after core"
  - "Niche only" — "Skip core, install specific framework/domain skills"
Default: Core only
```

问题：仅安装核心技能，还是包含利基/框架包？
选项：
  - "仅核心（推荐）" — "tdd、e2e、评估、验证、研究优先、安全、前端模式、压缩、跨职能 Anthropic 技能"
  - "核心 + 选定的利基" — "在核心之后添加框架/领域特定技能"
  - "仅利基" — "跳过核心，安装特定框架/领域技能"
默认值：仅核心

If the user chooses niche or core + niche, continue to category selection below and only include those niche skills they pick.
如果用户选择利基或核心 + 利基，继续下方类别选择，仅包含他们选择的利基技能。

### 2b: Choose Skill Categories
### 2b：选择技能类别

There are 7 selectable category groups below. The detailed confirmation lists that follow cover 45 skills across 8 categories, plus 1 standalone template. Use `AskUserQuestion` with `multiSelect: true`:
以下是7个可选类别组。后续详细确认列表涵盖8个类别45个技能，加1个独立模板。使用 `AskUserQuestion` 配合 `multiSelect: true`：

```
Question: "Which skill categories do you want to install?"
Options:
  - "Framework & Language" — "Django, Laravel, Spring Boot, Go, Python, Java, Frontend, Backend patterns"
  - "Database" — "PostgreSQL, ClickHouse, JPA/Hibernate patterns"
  - "Workflow & Quality" — "TDD, verification, learning, security review, compaction"
  - "Research & APIs" — "Deep research, Exa search, Claude API patterns"
  - "Social & Content Distribution" — "X/Twitter API, crossposting alongside content-engine"
  - "Media Generation" — "fal.ai image/video/audio alongside VideoDB"
  - "Orchestration" — "dmux multi-agent workflows"
  - "All skills" — "Install every available skill"
```

问题：您想安装哪些技能类别？
选项：
  - "框架和语言" — "Django、Laravel、Spring Boot、Go、Python、Java、前端、后端模式"
  - "数据库" — "PostgreSQL、ClickHouse、JPA/Hibernate 模式"
  - "工作流与质量" — "TDD、验证、学习、安全审查、压缩"
  - "研究和 API" — "深度研究、Exa 搜索、Claude API 模式"
  - "社交与内容分发" — "X/Twitter API、跨发布配合 content-engine"
  - "媒体生成" — "fal.ai 图像/视频/音频配合 VideoDB"
  - "编排" — "dmux 多智能体工作流"
  - "所有技能" — "安装所有可用技能"

### 2c: Confirm Individual Skills
### 2c：确认各技能

For each selected category, print the full list of skills below and ask the user to confirm or deselect specific ones. If the list exceeds 4 items, print the list as text and use `AskUserQuestion` with an "Install all listed" option plus "Other" for the user to paste specific names.
对于每个选定的类别，打印下方完整技能列表并让用户确认或取消选择特定技能。如果列表超过4项，打印列表为文本并使用 `AskUserQuestion` 提供"安装所有列出的"选项加"其他"供用户粘贴特定名称。

**Category: Framework & Language (21 skills)**
**类别：框架和语言（21个技能）**

| Skill | Description |
| 技能 | 描述 |
|-------|-------------|
| `backend-patterns` | Backend architecture, API design, server-side best practices for Node.js/Express/Next.js |
| `backend-patterns` | Node.js/Express/Next.js 的后端架构、API 设计、服务器端最佳实践 |
| `coding-standards` | Universal coding standards for TypeScript, JavaScript, React, Node.js |
| `coding-standards` | TypeScript、JavaScript、React、Node.js 的通用编码标准 |
| `django-patterns` | Django architecture, REST API with DRF, ORM, caching, signals, middleware |
| `django-patterns` | Django 架构、DRF REST API、ORM、缓存、信号、中间件 |
| `django-security` | Django security: auth, CSRF, SQL injection, XSS prevention |
| `django-security` | Django 安全：认证、CSRF、SQL 注入、XSS 防护 |
| `django-tdd` | Django testing with pytest-django, factory_boy, mocking, coverage |
| `django-tdd` | Django 测试：pytest-django、factory_boy、模拟、覆盖率 |
| `django-verification` | Django verification loop: migrations, linting, tests, security scans |
| `django-verification` | Django 验证循环：迁移、linting、测试、安全扫描 |
| `laravel-patterns` | Laravel architecture patterns: routing, controllers, Eloquent, queues, caching |
| `laravel-patterns` | Laravel 架构模式：路由、控制器、Eloquent、队列、缓存 |
| `laravel-security` | Laravel security: auth, policies, CSRF, mass assignment, rate limiting |
| `laravel-security` | Laravel 安全：认证、策略、CSRF、批量赋值、速率限制 |
| `laravel-tdd` | Laravel testing with PHPUnit and Pest, factories, fakes, coverage |
| `laravel-tdd` | Laravel 测试：PHPUnit 和 Pest、工厂、模拟、覆盖率 |
| `laravel-verification` | Laravel verification: linting, static analysis, tests, security scans |
| `laravel-verification` | Laravel 验证：linting、静态分析、测试、安全扫描 |
| `frontend-patterns` | React, Next.js, state management, performance, UI patterns |
| `frontend-patterns` | React、Next.js、状态管理、性能、UI 模式 |
| `frontend-slides` | Zero-dependency HTML presentations, style previews, and PPTX-to-web conversion |
| `frontend-slides` | 零依赖 HTML 演示文稿、样式预览和 PPTX 到 Web 转换 |
| `golang-patterns` | Idiomatic Go patterns, conventions for robust Go applications |
| `golang-patterns` | 惯用 Go 模式、健壮 Go 应用程序的约定 |
| `golang-testing` | Go testing: table-driven tests, subtests, benchmarks, fuzzing |
| `golang-testing` | Go 测试：表驱动测试、子测试、基准测试、模糊测试 |
| `java-coding-standards` | Java coding standards for Spring Boot: naming, immutability, Optional, streams |
| `java-coding-standards` | Spring Boot 的 Java 编码标准：命名、不可变性、Optional、流 |
| `python-patterns` | Pythonic idioms, PEP 8, type hints, best practices |
| `python-patterns` | Python 惯用语、PEP 8、类型提示、最佳实践 |
| `python-testing` | Python testing with pytest, TDD, fixtures, mocking, parametrization |
| `python-testing` | Python 测试：pytest、TDD、fixtures、模拟、参数化 |
| `springboot-patterns` | Spring Boot architecture, REST API, layered services, caching, async |
| `springboot-patterns` | Spring Boot 架构、REST API、分层服务、缓存、异步 |
| `springboot-security` | Spring Security: authn/authz, validation, CSRF, secrets, rate limiting |
| `springboot-security` | Spring Security：认证/授权、验证、CSRF、密钥、速率限制 |
| `springboot-tdd` | Spring Boot TDD with JUnit 5, Mockito, MockMvc, Testcontainers |
| `springboot-tdd` | Spring Boot TDD：JUnit 5、Mockito、MockMvc、Testcontainers |
| `springboot-verification` | Spring Boot verification: build, static analysis, tests, security scans |
| `springboot-verification` | Spring Boot 验证：构建、静态分析、测试、安全扫描 |

**Category: Database (3 skills)**
**类别：数据库（3个技能）**

| Skill | Description |
| 技能 | 描述 |
|-------|-------------|
| `clickhouse-io` | ClickHouse patterns, query optimization, analytics, data engineering |
| `clickhouse-io` | ClickHouse 模式、查询优化、分析、数据工程 |
| `jpa-patterns` | JPA/Hibernate entity design, relationships, query optimization, transactions |
| `jpa-patterns` | JPA/Hibernate 实体设计、关系、查询优化、事务 |
| `postgres-patterns` | PostgreSQL query optimization, schema design, indexing, security |
| `postgres-patterns` | PostgreSQL 查询优化、模式设计、索引、安全 |

**Category: Workflow & Quality (8 skills)**
**类别：工作流与质量（8个技能）**

| Skill | Description |
| 技能 | 描述 |
|-------|-------------|
| `continuous-learning` | Auto-extract reusable patterns from sessions as learned skills |
| `continuous-learning` | 从会话中自动提取可复用模式作为已学习技能 |
| `continuous-learning-v2` | Instinct-based learning with confidence scoring, evolves into skills/commands/agents |
| `continuous-learning-v2` | 基于本能的学习配合置信度评分，演化为技能/命令/智能体 |
| `eval-harness` | Formal evaluation framework for eval-driven development (EDD) |
| `eval-harness` | 用于评估驱动开发（EDD）的正式评估框架 |
| `iterative-retrieval` | Progressive context refinement for subagent context problem |
| `iterative-retrieval` | 用于子智能体上下文问题的渐进式上下文优化 |
| `security-review` | Security checklist: auth, input, secrets, API, payment features |
| `security-review` | 安全清单：认证、输入、密钥、API、支付功能 |
| `strategic-compact` | Suggests manual context compaction at logical intervals |
| `strategic-compact` | 建议在逻辑间隔手动压缩上下文 |
| `tdd-workflow` | Enforces TDD with 80%+ coverage: unit, integration, E2E |
| `tdd-workflow` | 强制 TDD 达到 80%+ 覆盖率：单元、集成、E2E |
| `verification-loop` | Verification and quality loop patterns |
| `verification-loop` | 验证和质量循环模式 |

**Category: Business & Content (5 skills)**
**类别：商业与内容（5个技能）**

| Skill | Description |
| 技能 | 描述 |
|-------|-------------|
| `article-writing` | Long-form writing in a supplied voice using notes, examples, or source docs |
| `article-writing` | 使用笔记、示例或源文档以提供的风格进行长篇写作 |
| `content-engine` | Multi-platform social content, scripts, and repurposing workflows |
| `content-engine` | 多平台社交内容、脚本和再利用工作流 |
| `market-research` | Source-attributed market, competitor, fund, and technology research |
| `market-research` | 有来源归因的市场、竞争对手、基金和技术研究 |
| `investor-materials` | Pitch decks, one-pagers, investor memos, and financial models |
| `investor-materials` | 路演幻灯片、单页材料、投资者备忘录和财务模型 |
| `investor-outreach` | Personalized investor cold emails, warm intros, and follow-ups |
| `investor-outreach` | 个性化投资者冷邮件、热情介绍和跟进 |

**Category: Research & APIs (3 skills)**
**类别：研究和 API（3个技能）**

| Skill | Description |
| 技能 | 描述 |
|-------|-------------|
| `deep-research` | Multi-source deep research using firecrawl and exa MCPs with cited reports |
| `deep-research` | 使用 firecrawl 和 exa MCP 进行多源深度研究并生成引用报告 |
| `exa-search` | Neural search via Exa MCP for web, code, company, and people research |
| `exa-search` | 通过 Exa MCP 进行神经搜索，用于网络、代码、公司和人物研究 |
| `claude-api` | Anthropic Claude API patterns: Messages, streaming, tool use, vision, batches, Agent SDK |
| `claude-api` | Anthropic Claude API 模式：消息、流式输出、工具调用、视觉、批处理、Agent SDK |

**Category: Social & Content Distribution (2 skills)**
**类别：社交与内容分发（2个技能）**

| Skill | Description |
| 技能 | 描述 |
|-------|-------------|
| `x-api` | X/Twitter API integration for posting, threads, search, and analytics |
| `x-api` | X/Twitter API 集成：发帖、线程、搜索和分析 |
| `crosspost` | Multi-platform content distribution with platform-native adaptation |
| `crosspost` | 带平台原生适配的多平台内容分发 |

**Category: Media Generation (2 skills)**
**类别：媒体生成（2个技能）**

| Skill | Description |
| 技能 | 描述 |
|-------|-------------|
| `fal-ai-media` | Unified AI media generation (image, video, audio) via fal.ai MCP |
| `fal-ai-media` | 通过 fal.ai MCP 统一 AI 媒体生成（图像、视频、音频） |
| `video-editing` | AI-assisted video editing for cutting, structuring, and augmenting real footage |
| `video-editing` | AI 辅助视频编辑：剪切、结构和增强真实素材 |

**Category: Orchestration (1 skill)**
**类别：编排（1个技能）**

| Skill | Description |
| 技能 | 描述 |
|-------|-------------|
| `dmux-workflows` | Multi-agent orchestration using dmux for parallel agent sessions |
| `dmux-workflows` | 使用 dmux 进行多智能体编排，支持并行智能体会话 |

**Standalone**
**独立**

| Skill | Description |
| 技能 | 描述 |
|-------|-------------|
| `project-guidelines-example` | Template for creating project-specific skills |
| `project-guidelines-example` | 用于创建项目特定技能的模板 |

### 2d: Execute Installation
### 2d：执行安装

For each selected skill, copy the entire skill directory:
对于每个选定的技能，复制整个技能目录：
```bash
cp -r $ECC_ROOT/skills/<skill-name> $TARGET/skills/
```

Note: `continuous-learning` and `continuous-learning-v2` have extra files (config.json, hooks, scripts) — ensure the entire directory is copied, not just SKILL.md.
注意：`continuous-learning` 和 `continuous-learning-v2` 有额外文件（config.json、hooks、scripts）——确保复制整个目录，而不仅仅是 SKILL.md。

---

## Step 3: Select & Install Rules
## 步骤 3：选择并安装规则

Use `AskUserQuestion` with `multiSelect: true`:
使用 `AskUserQuestion` 配合 `multiSelect: true`：

```
Question: "Which rule sets do you want to install?"
Options:
  - "Common rules (Recommended)" — "Language-agnostic principles: coding style, git workflow, testing, security, etc. (8 files)"
  - "TypeScript/JavaScript" — "TS/JS patterns, hooks, testing with Playwright (5 files)"
  - "Python" — "Python patterns, pytest, black/ruff formatting (5 files)"
  - "Go" — "Go patterns, table-driven tests, gofmt/staticcheck (5 files)"
```

问题：您想安装哪些规则集？
选项：
  - "通用规则（推荐）" — "语言无关的原则：编码风格、git 工作流、测试、安全等（8个文件）"
  - "TypeScript/JavaScript" — "TS/JS 模式、hooks、Playwright 测试（5个文件）"
  - "Python" — "Python 模式、pytest、black/ruff 格式化（5个文件）"
  - "Go" — "Go 模式、表驱动测试、gofmt/staticcheck（5个文件）"

Execute installation:
执行安装：
```bash
# Common rules (flat copy into rules/)
# 通用规则（平铺复制到 rules/）
cp -r $ECC_ROOT/rules/common/* $TARGET/rules/

# Language-specific rules (flat copy into rules/)
# 语言特定规则（平铺复制到 rules/）
cp -r $ECC_ROOT/rules/typescript/* $TARGET/rules/   # if selected
cp -r $ECC_ROOT/rules/python/* $TARGET/rules/        # if selected
cp -r $ECC_ROOT/rules/golang/* $TARGET/rules/        # if selected
```

**Important**: If the user selects any language-specific rules but NOT common rules, warn them:
**重要**：如果用户选择任何语言特定规则但不包括通用规则，警告他们：
> "Language-specific rules extend the common rules. Installing without common rules may result in incomplete coverage. Install common rules too?"
> "语言特定规则扩展通用规则。不安装通用规则可能导致覆盖不完整。也要安装通用规则吗？"

---

## Step 4: Post-Installation Verification
## 步骤 4：安装后验证

After installation, perform these automated checks:
安装后执行以下自动检查：

### 4a: Verify File Existence
### 4a：验证文件存在

List all installed files and confirm they exist at the target location:
列出所有已安装文件并确认它们在目标位置存在：
```bash
ls -la $TARGET/skills/
ls -la $TARGET/rules/
```

### 4b: Check Path References
### 4b：检查路径引用

Scan all installed `.md` files for path references:
扫描所有已安装的 `.md` 文件查找路径引用：
```bash
grep -rn "~/.claude/" $TARGET/skills/ $TARGET/rules/
grep -rn "../common/" $TARGET/rules/
grep -rn "skills/" $TARGET/skills/
```

**For project-level installs**, flag any references to `~/.claude/` paths:
**对于项目级安装**，标记任何对 `~/.claude/` 路径的引用：
- If a skill references `~/.claude/settings.json` — this is usually fine (settings are always user-level)
  - 如果技能引用 `~/.claude/settings.json` —— 这通常没问题（设置始终是用户级）
- If a skill references `~/.claude/skills/` or `~/.claude/rules/` — this may be broken if installed only at project level
  - 如果技能引用 `~/.claude/skills/` 或 `~/.claude/rules/` —— 如果仅安装到项目级可能会出问题
- If a skill references another skill by name — check that the referenced skill was also installed
  - 如果技能按名称引用另一个技能 —— 检查被引用的技能也被安装了

### 4c: Check Cross-References Between Skills
### 4c：检查技能间交叉引用

Some skills reference others. Verify these dependencies:
某些技能引用其他技能。验证这些依赖：
- `django-tdd` may reference `django-patterns`
  - `django-tdd` 可能引用 `django-patterns`
- `laravel-tdd` may reference `laravel-patterns`
  - `laravel-tdd` 可能引用 `laravel-patterns`
- `springboot-tdd` may reference `springboot-patterns`
  - `springboot-tdd` 可能引用 `springboot-patterns`
- `continuous-learning-v2` references `~/.claude/homunculus/` directory
  - `continuous-learning-v2` 引用 `~/.claude/homunculus/` 目录
- `python-testing` may reference `python-patterns`
  - `python-testing` 可能引用 `python-patterns`
- `golang-testing` may reference `golang-patterns`
  - `golang-testing` 可能引用 `golang-patterns`
- `crosspost` references `content-engine` and `x-api`
  - `crosspost` 引用 `content-engine` 和 `x-api`
- `deep-research` references `exa-search` (complementary MCP tools)
  - `deep-research` 引用 `exa-search`（互补的 MCP 工具）
- `fal-ai-media` references `videodb` (complementary media skill)
  - `fal-ai-media` 引用 `videodb`（互补的媒体技能）
- `x-api` references `content-engine` and `crosspost`
  - `x-api` 引用 `content-engine` 和 `crosspost`
- Language-specific rules reference `common/` counterparts
  - 语言特定规则引用 `common/` 对应文件

### 4d: Report Issues
### 4d：报告问题

For each issue found, report:
对于发现的每个问题，报告：
1. **File**: The file containing the problematic reference
   - **文件**：包含问题引用的文件
2. **Line**: The line number
   - **行**：行号
3. **Issue**: What's wrong (e.g., "references ~/.claude/skills/python-patterns but python-patterns was not installed")
   - **问题**：什么问题（例如，"引用 ~/.claude/skills/python-patterns 但 python-patterns 未安装"）
4. **Suggested fix**: What to do (e.g., "install python-patterns skill" or "update path to .claude/skills/")
   - **建议修复**：该怎么做（例如，"安装 python-patterns 技能"或"更新路径为 .claude/skills/"）

---

## Step 5: Optimize Installed Files (Optional)
## 步骤 5：优化已安装文件（可选）

Use `AskUserQuestion`:
使用 `AskUserQuestion`：

```
Question: "Would you like to optimize the installed files for your project?"
Options:
  - "Optimize skills" — "Remove irrelevant sections, adjust paths, tailor to your tech stack"
  - "Optimize rules" — "Adjust coverage targets, add project-specific patterns, customize tool configs"
  - "Optimize both" — "Full optimization of all installed files"
  - "Skip" — "Keep everything as-is"
```

问题：您想为项目优化已安装的文件吗？
选项：
  - "优化技能" — "移除不相关部分、调整路径、定制技术栈"
  - "优化规则" — "调整覆盖目标、添加项目特定模式、自定义工具配置"
  - "两者都优化" — "全面优化所有已安装文件"
  - "跳过" — "保持原样"

### If optimizing skills:
### 如果优化技能：
1. Read each installed SKILL.md
   - 阅读每个已安装的 SKILL.md
2. Ask the user what their project's tech stack is (if not already known)
   - 询问用户项目的技术栈（如果尚不清楚）
3. For each skill, suggest removals of irrelevant sections
   - 对于每个技能，建议移除不相关部分
4. Edit the SKILL.md files in-place at the installation target (NOT the source repo)
   - 在安装目标处就地编辑 SKILL.md 文件（不是源仓库）
5. Fix any path issues found in Step 4
   - 修复步骤 4 中发现的任何路径问题

### If optimizing rules:
### 如果优化规则：
1. Read each installed rule .md file
   - 阅读每个已安装的规则 .md 文件
2. Ask the user about their preferences:
   - 询问用户关于他们偏好的问题：
   - Test coverage target (default 80%)
     - 测试覆盖率目标（默认 80%）
   - Preferred formatting tools
     - 首选格式化工具
   - Git workflow conventions
     - Git 工作流约定
   - Security requirements
     - 安全要求
3. Edit the rule files in-place at the installation target
   - 在安装目标处就地编辑规则文件

**Critical**: Only modify files in the installation target (`$TARGET/`), NEVER modify files in the source ECC repository (`$ECC_ROOT/`).
**关键**：仅修改安装目标中的文件（`$TARGET/`），切勿修改源 ECC 仓库中的文件（`$ECC_ROOT/`）。

---

## Step 6: Installation Summary
## 步骤 6：安装摘要

Clean up the cloned repository from `/tmp`:
清理 `/tmp` 中的克隆仓库：

```bash
rm -rf /tmp/everything-claude-code
```

Then print a summary report:
然后打印摘要报告：

```
## ECC Installation Complete

### Installation Target
- Level: [user-level / project-level / both]
- Path: [target path]

### Skills Installed ([count])
- skill-1, skill-2, skill-3, ...

### Rules Installed ([count])
- common (8 files)
- typescript (5 files)
- ...

### Verification Results
- [count] issues found, [count] fixed
- [list any remaining issues]

### Optimizations Applied
- [list changes made, or "None"]
```

## Troubleshooting
## 故障排除

### "Skills not being picked up by Claude Code"
### "技能未被 Claude Code 识别"
- Verify the skill directory contains a `SKILL.md` file (not just loose .md files)
  - 验证技能目录包含 `SKILL.md` 文件（不仅仅是松散的 .md 文件）
- For user-level: check `~/.claude/skills/<skill-name>/SKILL.md` exists
  - 对于用户级：检查 `~/.claude/skills/<skill-name>/SKILL.md` 存在
- For project-level: check `.claude/skills/<skill-name>/SKILL.md` exists
  - 对于项目级：检查 `.claude/skills/<skill-name>/SKILL.md` 存在

### "Rules not working"
### "规则不工作"
- Rules are flat files, not in subdirectories: `$TARGET/rules/coding-style.md` (correct) vs `$TARGET/rules/common/coding-style.md` (incorrect for flat install)
  - 规则是平铺文件，不在子目录中：`$TARGET/rules/coding-style.md`（正确）vs `$TARGET/rules/common/coding-style.md`（平铺安装不正确）
- Restart Claude Code after installing rules
  - 安装规则后重启 Claude Code

### "Path reference errors after project-level install"
### "项目级安装后路径引用错误"
- Some skills assume `~/.claude/` paths. Run Step 4 verification to find and fix these.
  - 某些技能假设 `~/.claude/` 路径。运行步骤 4 验证来查找和修复这些。
- For `continuous-learning-v2`, the `~/.claude/homunculus/` directory is always user-level — this is expected and not an error.
  - 对于 `continuous-learning-v2`，`~/.claude/homunculus/` 目录始终是用户级——这是预期的，不是错误。
