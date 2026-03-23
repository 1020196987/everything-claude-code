---
name: prompt-optimizer
description: >-
  Analyze raw prompts, identify intent and gaps, match ECC components
  (skills/commands/agents/hooks), and output a ready-to-paste optimized
  prompt. Advisory role only — never executes the task itself.
  TRIGGER when: user says "optimize prompt", "improve my prompt",
  "how to write a prompt for", "help me prompt", "rewrite this prompt",
  or explicitly asks to enhance prompt quality. Also triggers on Chinese
  equivalents: "优化prompt", "改进prompt", "怎么写prompt", "帮我优化这个指令".
  DO NOT TRIGGER when: user wants the task executed directly, or says
  "just do it" / "直接做". DO NOT TRIGGER when user says "优化代码",
  "优化性能", "optimize performance", "optimize this code" — those are
  refactoring/performance tasks, not prompt optimization.
description zh-CN: >-
  分析原始提示词，识别意图和缺口，匹配 ECC 组件（skills/commands/agents/hooks），
  并输出可直接粘贴的优化提示词。仅作咨询角色 — 绝不执行任务本身。
  触发条件：用户说 "optimize prompt"、"improve my prompt"、"how to write a prompt for"、
  "help me prompt"、"rewrite this prompt"，或明确要求增强提示词质量。
  也对中文等价词触发："优化prompt"、"改进prompt"、"怎么写prompt"、"帮我优化这个指令"。
  不触发条件：用户希望直接执行任务，或说 "just do it" / "直接做"。
  用户说 "优化代码"、"优化性能"、"optimize performance"、"optimize this code" 时不触发 —
  那些是重构/性能任务，不是提示词优化。
origin: community
metadata:
  author: YannJY02
  version: "1.0.0"
---

# Prompt Optimizer

## Prompt Optimizer
## 提示词优化器

Analyze a draft prompt, critique it, match it to ECC ecosystem components,
and output a complete optimized prompt the user can paste and run.
分析草稿提示词，对其进行批评，将其与 ECC 生态系统组件匹配，
并输出用户可以直接粘贴和运行的完整优化提示词。

## When to Use

## When to Use
## 何时使用

- User says "optimize this prompt", "improve my prompt", "rewrite this prompt"
- 用户说 "optimize this prompt"、"improve my prompt"、"rewrite this prompt"
- User says "help me write a better prompt for..."
- 用户说 "help me write a better prompt for..."
- User says "what's the best way to ask Claude Code to..."
- 用户说 "what's the best way to ask Claude Code to..."
- User says "优化prompt", "改进prompt", "怎么写prompt", "帮我优化这个指令"
- 用户说 "优化prompt"、"改进prompt"、"怎么写prompt"、"帮我优化这个指令"
- User pastes a draft prompt and asks for feedback or enhancement
- 用户粘贴草稿提示词并请求反馈或增强
- User says "I don't know how to prompt for this"
- 用户说 "I don't know how to prompt for this"
- User says "how should I use ECC for..."
- 用户说 "how should I use ECC for..."
- User explicitly invokes `/prompt-optimize`
- 用户显式调用 `/prompt-optimize`

### Do Not Use When

### Do Not Use When
### 不使用场景

- User wants the task done directly (just execute it)
- 用户希望直接完成任务（直接执行）
- User says "优化代码", "优化性能", "optimize this code", "optimize performance" — these are refactoring tasks, not prompt optimization
- 用户说 "优化代码"、"优化性能"、"optimize this code"、"optimize performance" — 这些是重构任务，不是提示词优化
- User is asking about ECC configuration (use `configure-ecc` instead)
- 用户询问 ECC 配置（改用 `configure-ecc`）
- User wants a skill inventory (use `skill-stocktake` instead)
- 用户想要技能清单（改用 `skill-stocktake`）
- User says "just do it" or "直接做"
- 用户说 "just do it" 或 "直接做"

## How It Works

## How It Works
## 工作原理

**Advisory only — do not execute the user's task.**
**仅作咨询 — 不要执行用户的任务。**

Do NOT write code, create files, run commands, or take any implementation
action. Your ONLY output is an analysis plus an optimized prompt.
不要写代码、创建文件、运行命令或采取任何实现操作。你的唯一输出是分析加上优化后的提示词。

If the user says "just do it", "直接做", or "don't optimize, just execute",
do not switch into implementation mode inside this skill. Tell the user this
skill only produces optimized prompts, and instruct them to make a normal
task request if they want execution instead.
如果用户说 "just do it"、"直接做" 或 "don't optimize, just execute"，不要在此技能内切换到实现模式。告诉用户此技能只产生优化提示词，并指示他们如果想要执行就提出正常的任务请求。

Run this 6-phase pipeline sequentially. Present results using the Output Format below.
按顺序运行此 6 阶段管道。使用下方的输出格式呈现结果。

### Analysis Pipeline

### Analysis Pipeline
### 分析管道

### Phase 0: Project Detection

### Phase 0: Project Detection
### 阶段 0：项目检测

Before analyzing the prompt, detect the current project context:
在分析提示词之前，检测当前项目上下文：

1. Check if a `CLAUDE.md` exists in the working directory — read it for project conventions
1. 检查工作目录中是否存在 `CLAUDE.md` — 阅读它以了解项目约定
2. Detect tech stack from project files:
2. 从项目文件检测技术栈：
   - `package.json` -> Node.js / TypeScript / React / Next.js
   - `go.mod` -> Go
   - `pyproject.toml` / `requirements.txt` -> Python
   - `Cargo.toml` -> Rust
   - `build.gradle` / `pom.xml` -> Java / Kotlin / Spring Boot
   - `Package.swift` -> Swift
   - `Gemfile` -> Ruby
   - `composer.json` -> PHP
   - `*.csproj` / `*.sln` -> .NET
   - `Makefile` / `CMakeLists.txt` -> C / C++
   - `cpanfile` / `Makefile.PL` -> Perl
3. Note detected tech stack for use in Phase 3 and Phase 4
3. 记录检测到的技术栈以用于阶段 3 和阶段 4

If no project files are found (e.g., the prompt is abstract or for a new project),
skip detection and flag "tech stack unknown" in Phase 4.
如果未找到项目文件（例如提示词是抽象的或针对新项目），
跳过检测并在阶段 4 标记"技术栈未知"。

### Phase 1: Intent Detection

### Phase 1: Intent Detection
### 阶段 1：意图检测

Classify the user's task into one or more categories:
将用户任务分类到一个或多个类别：

| Category | Signal Words | Example |
| 类别 | 信号词 | 示例 |
|----------|-------------|---------|
| New Feature / 新功能 | build, create, add, implement, 创建, 实现, 添加 | "Build a login page" / "构建登录页面" |
| Bug Fix / 修复缺陷 | fix, broken, not working, error, 修复, 报错 | "Fix the auth flow" / "修复认证流程" |
| Refactor / 重构 | refactor, clean up, restructure, 重构, 整理 | "Refactor the API layer" / "重构 API 层" |
| Research / 研究 | how to, what is, explore, investigate, 怎么, 如何 | "How to add SSO" / "如何添加 SSO" |
| Testing / 测试 | test, coverage, verify, 测试, 覆盖率 | "Add tests for the cart" / "为购物车添加测试" |
| Review / 审查 | review, audit, check, 审查, 检查 | "Review my PR" / "审查我的 PR" |
| Documentation / 文档 | document, update docs, 文档 | "Update the API docs" / "更新 API 文档" |
| Infrastructure / 基础设施 | deploy, CI, docker, database, 部署, 数据库 | "Set up CI/CD pipeline" / "设置 CI/CD 流水线" |
| Design / 设计 | design, architecture, plan, 设计, 架构 | "Design the data model" / "设计数据模型" |

### Phase 2: Scope Assessment

### Phase 2: Scope Assessment
### 阶段 2：范围评估

If Phase 0 detected a project, use codebase size as a signal. Otherwise, estimate
from the prompt description alone and mark the estimate as uncertain.
如果阶段 0 检测到项目，使用代码库大小作为信号。否则，仅从提示词描述估计并标记估计为不确定。

| Scope | Heuristic | Orchestration |
| 范围 | 启发式方法 | 协调方式 |
|-------|-----------|---------------|
| TRIVIAL | Single file, < 50 lines | Direct execution / 直接执行 |
| LOW | Single component or module | Single command or skill / 单命令或技能 |
| MEDIUM | Multiple components, same domain | Command chain + /verify / 命令链 + /verify |
| HIGH | Cross-domain, 5+ files | /plan first, then phased execution / 先 /plan，然后分阶段执行 |
| EPIC | Multi-session, multi-PR, architectural shift | Use blueprint skill for multi-session plan / 使用 blueprint 技能进行多会话计划 |

### Phase 3: ECC Component Matching

### Phase 3: ECC Component Matching
### 阶段 3：ECC 组件匹配

Map intent + scope + tech stack (from Phase 0) to specific ECC components.
将意图 + 范围 + 技术栈（来自阶段 0）映射到特定的 ECC 组件。

#### By Intent Type

#### By Intent Type
#### 按意图类型

| Intent | Commands | Skills | Agents |
| 意图 | 命令 | 技能 | Agent |
|--------|----------|--------|--------|
| New Feature / 新功能 | /plan, /tdd, /code-review, /verify | tdd-workflow, verification-loop | planner, tdd-guide, code-reviewer |
| Bug Fix / 修复缺陷 | /tdd, /build-fix, /verify | tdd-workflow | tdd-guide, build-error-resolver |
| Refactor / 重构 | /refactor-clean, /code-review, /verify | verification-loop | refactor-cleaner, code-reviewer |
| Research / 研究 | /plan | search-first, iterative-retrieval | — |
| Testing / 测试 | /tdd, /e2e, /test-coverage | tdd-workflow, e2e-testing | tdd-guide, e2e-runner |
| Review / 审查 | /code-review | security-review | code-reviewer, security-reviewer |
| Documentation / 文档 | /update-docs, /update-codemaps | — | doc-updater |
| Infrastructure / 基础设施 | /plan, /verify | docker-patterns, deployment-patterns, database-migrations | architect |
| Design (MEDIUM-HIGH) / 设计（中-高） | /plan | — | planner, architect |
| Design (EPIC) / 设计（宏大） | — | blueprint (invoke as skill) / blueprint（作为技能调用） | planner, architect |

#### By Tech Stack

#### By Tech Stack
#### 按技术栈

| Tech Stack | Skills to Add | Agent |
| 技术栈 | 要添加的技能 | Agent |
|------------|--------------|-------|
| Python / Django | django-patterns, django-tdd, django-security, django-verification, python-patterns, python-testing | python-reviewer |
| Go | golang-patterns, golang-testing | go-reviewer, go-build-resolver |
| Spring Boot / Java | springboot-patterns, springboot-tdd, springboot-security, springboot-verification, java-coding-standards, jpa-patterns | code-reviewer |
| Kotlin / Android | kotlin-coroutines-flows, compose-multiplatform-patterns, android-clean-architecture | kotlin-reviewer |
| TypeScript / React | frontend-patterns, backend-patterns, coding-standards | code-reviewer |
| Swift / iOS | swiftui-patterns, swift-concurrency-6-2, swift-actor-persistence, swift-protocol-di-testing | code-reviewer |
| PostgreSQL | postgres-patterns, database-migrations | database-reviewer |
| Perl | perl-patterns, perl-testing, perl-security | code-reviewer |
| C++ | cpp-coding-standards, cpp-testing | code-reviewer |
| Other / Unlisted / 其他/未列出 | coding-standards (universal) / coding-standards（通用） | code-reviewer |

### Phase 4: Missing Context Detection

### Phase 4: Missing Context Detection
### 阶段 4：缺失上下文检测

Scan the prompt for missing critical information. Check each item and mark
whether Phase 0 auto-detected it or the user must supply it:
扫描提示词中缺失的关键信息。检查每个项目并标记阶段 0 是否自动检测到或用户必须提供：

- [ ] **Tech stack** — Detected in Phase 0, or must user specify?
- [ ] **技术栈** — 在阶段 0 检测到，还是用户必须指定？
- [ ] **Target scope** — Files, directories, or modules mentioned?
- [ ] **目标范围** — 是否提到了文件、目录或模块？
- [ ] **Acceptance criteria** — How to know the task is done?
- [ ] **验收标准** — 如何知道任务完成了？
- [ ] **Error handling** — Edge cases and failure modes addressed?
- [ ] **错误处理** — 是否处理了边界情况和失败模式？
- [ ] **Security requirements** — Auth, input validation, secrets?
- [ ] **安全要求** — 认证、输入验证、密钥？
- [ ] **Testing expectations** — Unit, integration, E2E?
- [ ] **测试期望** — 单元、集成、E2E？
- [ ] **Performance constraints** — Load, latency, resource limits?
- [ ] **性能约束** — 负载、延迟、资源限制？
- [ ] **UI/UX requirements** — Design specs, responsive, a11y? (if frontend)
- [ ] **UI/UX 要求** — 设计规范、响应式、无障碍？（如果是前端）
- [ ] **Database changes** — Schema, migrations, indexes? (if data layer)
- [ ] **数据库变更** — 模式、迁移、索引？（如果是数据层）
- [ ] **Existing patterns** — Reference files or conventions to follow?
- [ ] **现有模式** — 要遵循的参考文件或约定？
- [ ] **Scope boundaries** — What NOT to do?
- [ ] **范围边界** — 什么不要做？

**If 3+ critical items are missing**, ask the user up to 3 clarification
questions before generating the optimized prompt. Then incorporate the
answers into the optimized prompt.
**如果缺少 3+ 个关键项目**，在生成优化提示词之前最多向用户提出 3 个澄清问题。然后将答案纳入优化提示词。

### Phase 5: Workflow & Model Recommendation

### Phase 5: Workflow & Model Recommendation
### 阶段 5：工作流和模型推荐

Determine where this prompt sits in the development lifecycle:
确定此提示词在开发生命周期中的位置：

```
Research → Plan → Implement (TDD) → Review → Verify → Commit
研究 → 计划 → 实现（TDD） → 审查 → 验证 → 提交
```

For MEDIUM+ tasks, always start with /plan. For EPIC tasks, use blueprint skill.
对于 MEDIUM+ 任务，始终从 /plan 开始。对于 EPIC 任务，使用 blueprint 技能。

**Model recommendation** (include in output):
**模型推荐**（包含在输出中）：

| Scope | Recommended Model | Rationale |
| 范围 | 推荐模型 | 理由 |
|-------|------------------|-----------|
| TRIVIAL-LOW | Sonnet 4.6 | Fast, cost-efficient for simple tasks / 快速、成本高效，适合简单任务 |
| MEDIUM | Sonnet 4.6 | Best coding model for standard work / 最佳编码模型，适合标准工作 |
| HIGH | Sonnet 4.6 (main) + Opus 4.6 (planning) | Opus for architecture, Sonnet for implementation / Opus 做架构，Sonnet 做实现 |
| EPIC | Opus 4.6 (blueprint) + Sonnet 4.6 (execution) | Deep reasoning for multi-session planning / 多会话计划的深度推理 |

**Multi-prompt splitting** (for HIGH/EPIC scope):
**多提示词拆分**（适用于 HIGH/EPIC 范围）：

For tasks that exceed a single session, split into sequential prompts:
对于超出单个会话的任务，拆分为顺序提示词：
- Prompt 1: Research + Plan (use search-first skill, then /plan)
- 提示词 1：研究 + 计划（使用 search-first 技能，然后 /plan）
- Prompt 2-N: Implement one phase per prompt (each ends with /verify)
- 提示词 2-N：每个提示词实现一个阶段（每个以 /verify 结束）
- Final Prompt: Integration test + /code-review across all phases
- 最终提示词：集成测试 + 跨所有阶段的 /code-review
- Use /save-session and /resume-session to preserve context between sessions
- 使用 /save-session 和 /resume-session 在会话之间保持上下文

---

## Output Format

## Output Format
## 输出格式

Present your analysis in this exact structure. Respond in the same language
as the user's input.
以此确切结构呈现你的分析。用与用户输入相同的语言回复。

### Section 1: Prompt Diagnosis

### Section 1: Prompt Diagnosis
### 第 1 部分：提示词诊断

**Strengths:** List what the original prompt does well.
**优点：** 列出原始提示词做得好的地方。

**Issues:**
**问题：**

| Issue | Impact | Suggested Fix |
| 问题 | 影响 | 建议修复 |
|-------|--------|---------------|
| (problem) / （问题） | (consequence) / （后果） | (how to fix) / （如何修复） |

**Needs Clarification:** Numbered list of questions the user should answer.
If Phase 0 auto-detected the answer, state it instead of asking.
**需要澄清：** 用户应回答的编号问题列表。如果阶段 0 自动检测到答案，说明它而不是提问。

### Section 2: Recommended ECC Components

### Section 2: Recommended ECC Components
### 第 2 部分：推荐的 ECC 组件

| Type | Component | Purpose |
| 类型 | 组件 | 目的 |
|------|-----------|---------|
| Command / 命令 | /plan | Plan architecture before coding / 编码前规划架构 |
| Skill / 技能 | tdd-workflow | TDD methodology guidance / TDD 方法论指导 |
| Agent | code-reviewer | Post-implementation review / 实现后审查 |
| Model / 模型 | Sonnet 4.6 | Recommended for this scope / 推荐用于此范围 |

### Section 3: Optimized Prompt — Full Version

### Section 3: Optimized Prompt — Full Version
### 第 3 部分：优化提示词 — 完整版

Present the complete optimized prompt inside a single fenced code block.
The prompt must be self-contained and ready to copy-paste. Include:
在单个带 fence 的代码块中呈现完整的优化提示词。
提示词必须是自包含的且可随时复制粘贴。包括：

- Clear task description with context
- 清晰的带上下文的任务描述
- Tech stack (detected or specified)
- 技术栈（检测到或指定的）
- /command invocations at the right workflow stages
- 在正确的工作流阶段调用 /command
- Acceptance criteria
- 验收标准
- Verification steps
- 验证步骤
- Scope boundaries (what NOT to do)
- 范围边界（什么不要做）

For items that reference blueprint, write: "Use the blueprint skill to..."
(not `/blueprint`, since blueprint is a skill, not a command).
对于引用 blueprint 的项目，写："Use the blueprint skill to..."
（不是 `/blueprint`，因为 blueprint 是技能，不是命令）。

### Section 4: Optimized Prompt — Quick Version

### Section 4: Optimized Prompt — Quick Version
### 第 4 部分：优化提示词 — 快速版

A compact version for experienced ECC users. Vary by intent type:
供有经验的 ECC 用户的紧凑版本。按意图类型变化：

| Intent | Quick Pattern |
| 意图 | 快速模式 |
|--------|--------------|
| New Feature / 新功能 | `/plan [feature]. /tdd to implement. /code-review. /verify.` |
| Bug Fix / 修复缺陷 | `/tdd — write failing test for [bug]. Fix to green. /verify.` |
| Refactor / 重构 | `/refactor-clean [scope]. /code-review. /verify.` |
| Research / 研究 | `Use search-first skill for [topic]. /plan based on findings.` |
| Testing / 测试 | `/tdd [module]. /e2e for critical flows. /test-coverage.` |
| Review / 审查 | `/code-review. Then use security-reviewer agent.` |
| Docs / 文档 | `/update-docs. /update-codemaps.` |
| EPIC | `Use blueprint skill for "[objective]". Execute phases with /verify gates.` |

### Section 5: Enhancement Rationale

### Section 5: Enhancement Rationale
### 第 5 部分：增强理由

| Enhancement | Reason |
| 增强项 | 理由 |
|-------------|--------|
| (what was added) / （添加了什么） | (why it matters) / （为什么重要） |

### Footer

### Footer
### 页脚

> Not what you need? Tell me what to adjust, or make a normal task request
> if you want execution instead of prompt optimization.
> 不是你需要的？告诉我需要调整什么，或者如果你想要执行而非提示词优化，请提出正常的任务请求。

---

## Examples

## Examples
## 示例

### Trigger Examples

### Trigger Examples
### 触发示例

- "Optimize this prompt for ECC"
- "Rewrite this prompt so Claude Code uses the right commands"
- "帮我优化这个指令"
- "How should I prompt ECC for this task?"

### Example 1: Vague Chinese Prompt (Project Detected)

### Example 1: Vague Chinese Prompt (Project Detected)
### 示例 1：模糊的中文提示词（检测到项目）

**User input:**
**用户输入：**
```
帮我写一个用户登录页面
```

**Phase 0 detects:** `package.json` with Next.js 15, TypeScript, Tailwind CSS
**阶段 0 检测到：** Next.js 15、TypeScript、Tailwind CSS 的 `package.json`

**Optimized Prompt (Full):**
**优化提示词（完整版）：**
```
使用项目现有技术栈（Next.js 15 + TypeScript + Tailwind CSS）实现用户登录页面。

技术要求：
- 沿用项目现有的组件结构和路由约定
- 表单验证使用项目中已有的验证方案（检查是否已用 Zod/Yup/其他）
- 认证方式：沿用项目现有认证方案（如无，默认 JWT）
- 包含：邮箱/密码登录表单、表单验证、错误提示、加载状态、响应式布局

工作流：
1. /plan 先规划组件结构和认证流程，参考现有页面的模式
2. /tdd 测试先行：编写登录表单的单元测试和认证流程的集成测试
3. 实现登录页面和认证逻辑
4. /code-review 审查实现
5. /verify 验证所有测试通过且页面正常渲染

安全要求：
- 密码不明文传输
- 防止暴力破解（rate limiting）
- XSS 防护
- CSRF token

验收标准：
- 所有测试通过，覆盖率 80%+
- 页面在移动端和桌面端正常渲染
- 登录成功跳转到 dashboard，失败显示错误信息

不要做：
- 不要实现注册页面
- 不要实现忘记密码功能
- 不要修改现有的路由结构
```

### Example 2: Moderate English Prompt

### Example 2: Moderate English Prompt
### 示例 2：中等复杂度的英文提示词

**User input:**
**用户输入：**
```
Add a REST API endpoint for user profile updates with validation
```

**Phase 0 detects:** `go.mod` with Go 1.22, Chi router
**阶段 0 检测到：** Go 1.22、Chi 路由器的 `go.mod`

**Optimized Prompt (Full):**
**优化提示词（完整版）：**
```
Add a REST API endpoint for user profile updates (PATCH /api/users/:id).

Tech stack: Go 1.22 + Chi router (detected from project)

Requirements:
- PATCH /api/users/:id — partial update of user profile
- Input validation for fields: name, email, avatar_url, bio
- Auth: require valid token, users can only update own profile
- Return 200 with updated user on success
- Return 400 with validation errors on invalid input
- Return 401/403 for auth failures
- Follow existing API patterns in the codebase

Workflow:
1. /plan the endpoint structure, middleware chain, and validation logic
2. /tdd — write table-driven tests for success, validation failure, auth failure, not-found
3. Implement following existing handler patterns
4. /go-review
5. /verify — run full test suite, confirm no regressions

Do not:
- Modify existing endpoints
- Change the database schema (use existing user table)
- Add new dependencies without checking existing ones first (use search-first skill)
```

### Example 3: EPIC Project

### Example 3: EPIC Project
### 示例 3：宏大项目

**User input:**
**用户输入：**
```
Migrate our monolith to microservices
```

**Optimized Prompt (Full):**
**优化提示词（完整版）：**
```
Use the blueprint skill to plan: "Migrate monolith to microservices architecture"

Before executing, answer these questions in the blueprint:
1. Which domain boundaries exist in the current monolith?
2. Which service should be extracted first (lowest coupling)?
3. Communication pattern: REST APIs, gRPC, or event-driven (Kafka/RabbitMQ)?
4. Database strategy: shared DB initially or database-per-service from start?
5. Deployment target: Kubernetes, Docker Compose, or serverless?

The blueprint should produce phases like:
- Phase 1: Identify service boundaries and create domain map
- Phase 2: Set up infrastructure (API gateway, service mesh, CI/CD per service)
- Phase 3: Extract first service (strangler fig pattern)
- Phase 4: Verify with integration tests, then extract next service
- Phase N: Decommission monolith

Each phase = 1 PR, with /verify gates between phases.
Use /save-session between phases. Use /resume-session to continue.
Use git worktrees for parallel service extraction when dependencies allow.

Recommended: Opus 4.6 for blueprint planning, Sonnet 4.6 for phase execution.
```

---

## Related Components

## Related Components
## 相关组件

| Component | When to Reference |
| 组件 | 何时参考 |
|-----------|------------------|
| `configure-ecc` | User hasn't set up ECC yet / 用户尚未设置 ECC |
| `skill-stocktake` | Audit which components are installed (use instead of hardcoded catalog) / 审查安装了哪些组件（使用它而非硬编码目录） |
| `search-first` | Research phase in optimized prompts / 优化提示词中的研究阶段 |
| `blueprint` | EPIC-scope optimized prompts (invoke as skill, not command) / 宏大范围的优化提示词（作为技能调用，不是命令） |
| `strategic-compact` | Long session context management / 长会话上下文管理 |
| `cost-aware-llm-pipeline` | Token optimization recommendations / Token 优化建议 |
