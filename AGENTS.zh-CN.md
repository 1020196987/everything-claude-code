# Everything Claude Code (ECC) — Agent Instructions
# Everything Claude Code (ECC) — Agent 指令

This is a **production-ready AI coding plugin** providing 25 specialized agents, 108 skills, 57 commands, and automated hook workflows for software development.
这是一个**生产就绪的 AI 编码插件**，提供 25 个专业 agents、108 skills、57 commands，以及用于软件开发的自动化 hook 工作流。

## Core Principles
## 核心原则

1. **Agent-First** — Delegate to specialized agents for domain tasks
1. **Agent 优先** — 将领域任务委托给专业 agents
2. **Test-Driven** — Write tests before implementation, 80%+ coverage required
2. **测试驱动** — 在实现之前编写测试，需要 80%+ 覆盖率
3. **Security-First** — Never compromise on security; validate all inputs
3. **安全优先** — 永不妥协安全；验证所有输入
4. **Immutability** — Always create new objects, never mutate existing ones
4. **不可变性** — 始终创建新对象，永不改变现有对象
5. **Plan Before Execute** — Plan complex features before writing code
5. **先计划后执行** — 在编写代码之前规划复杂功能

## Available Agents
## 可用 Agents

| Agent | Purpose | When to Use |
|-------|---------|-------------|
| planner | Implementation planning | Complex features, refactoring |
| architect | System design and scalability | Architectural decisions |
| tdd-guide | Test-driven development | New features, bug fixes |
| code-reviewer | Code quality and maintainability | After writing/modifying code |
| security-reviewer | Vulnerability detection | Before commits, sensitive code |
| build-error-resolver | Fix build/type errors | When build fails |
| e2e-runner | End-to-end Playwright testing | Critical user flows |
| refactor-cleaner | Dead code cleanup | Code maintenance |
| doc-updater | Documentation and codemaps | Updating docs |
| go-reviewer | Go code review | Go projects |
| go-build-resolver | Go build errors | Go build failures |
| kotlin-reviewer | Kotlin code review | Kotlin/Android/KMP projects |
| kotlin-build-resolver | Kotlin/Gradle build errors | Kotlin build failures |
| database-reviewer | PostgreSQL/Supabase specialist | Schema design, query optimization |
| python-reviewer | Python code review | Python projects |
| java-reviewer | Java and Spring Boot code review | Java/Spring Boot projects |
| java-build-resolver | Java/Maven/Gradle build errors | Java build failures |
| chief-of-staff | Communication triage and drafts | Multi-channel email, Slack, LINE, Messenger |
| loop-operator | Autonomous loop execution | Run loops safely, monitor stalls, intervene |
| harness-optimizer | Harness config tuning | Reliability, cost, throughput |
| rust-reviewer | Rust code review | Rust projects |
| rust-build-resolver | Rust build errors | Rust build failures |
| Agent | 用途 | 使用时机 |
|-------|---------|-------------|
| planner | 实现规划 | 复杂功能、重构 |
| architect | 系统设计和可扩展性 | 架构决策 |
| tdd-guide | 测试驱动开发 | 新功能、bug 修复 |
| code-reviewer | 代码质量和可维护性 | 编写/修改代码后 |
| security-reviewer | 漏洞检测 | 提交前、敏感代码 |
| build-error-resolver | 修复构建/类型错误 | 构建失败时 |
| e2e-runner | 端到端 Playwright 测试 | 关键用户流程 |
| refactor-cleaner | 死代码清理 | 代码维护 |
| doc-updater | 文档和 codemaps | 更新文档 |
| go-reviewer | Go 代码审查 | Go 项目 |
| go-build-resolver | Go 构建错误 | Go 构建失败 |
| kotlin-reviewer | Kotlin 代码审查 | Kotlin/Android/KMP 项目 |
| kotlin-build-resolver | Kotlin/Gradle 构建错误 | Kotlin 构建失败 |
| database-reviewer | PostgreSQL/Supabase 专家 | 模式设计、查询优化 |
| python-reviewer | Python 代码审查 | Python 项目 |
| java-reviewer | Java 和 Spring Boot 代码审查 | Java/Spring Boot 项目 |
| java-build-resolver | Java/Maven/Gradle 构建错误 | Java 构建失败 |
| chief-of-staff | 通信分类和草稿 | 多渠道 email、Slack、LINE、Messenger |
| loop-operator | 自主循环执行 | 安全运行循环、监控停顿、干预 |
| harness-optimizer | Harness 配置调优 | 可靠性、成本、吞吐量 |
| rust-reviewer | Rust 代码审查 | Rust 项目 |
| rust-build-resolver | Rust 构建错误 | Rust 构建失败 |

## Agent Orchestration
## Agent 编排

Use agents proactively without user prompt:
主动使用 agents，无需用户提示：

- Complex feature requests → **planner**
- 复杂功能请求 → **planner**
- Code just written/modified → **code-reviewer**
- 刚编写/修改代码 → **code-reviewer**
- Bug fix or new feature → **tdd-guide**
- Bug 修复或新功能 → **tdd-guide**
- Architectural decision → **architect**
- 架构决策 → **architect**
- Security-sensitive code → **security-reviewer**
- 安全敏感代码 → **security-reviewer**
- Multi-channel communication triage → **chief-of-staff**
- 多渠道通信分类 → **chief-of-staff**
- Autonomous loops / loop monitoring → **loop-operator**
- 自主循环/循环监控 → **loop-operator**
- Harness config reliability and cost → **harness-optimizer**
- Harness 配置可靠性和成本 → **harness-optimizer**

Use parallel execution for independent operations — launch multiple agents simultaneously.
对独立操作使用并行执行——同时启动多个 agents。

## Security Guidelines
## 安全指南

**Before ANY commit:**
**提交前必须：**

- No hardcoded secrets (API keys, passwords, tokens)
- 无硬编码 secrets（API 密钥、密码、令牌）
- All user inputs validated
- 验证所有用户输入
- SQL injection prevention (parameterized queries)
- 防止 SQL 注入（参数化查询）
- XSS prevention (sanitized HTML)
- 防止 XSS（清理 HTML）
- CSRF protection enabled
- 启用 CSRF 保护
- Authentication/authorization verified
- 验证认证/授权
- Rate limiting on all endpoints
- 所有端点启用速率限制
- Error messages don't leak sensitive data
- 错误消息不泄露敏感数据

**Secret management:** NEVER hardcode secrets. Use environment variables or a secret manager. Validate required secrets at startup. Rotate any exposed secrets immediately.
**Secret 管理：** 永不硬编码 secrets。使用环境变量或 secret 管理器。启动时验证所需的 secrets。立即轮换任何暴露的 secrets。

**If security issue found:** STOP → use security-reviewer agent → fix CRITICAL issues → rotate exposed secrets → review codebase for similar issues.
**发现安全问题：** 停止 → 使用 security-reviewer agent → 修复关键问题 → 轮换暴露的 secrets → 审查代码库中的类似问题。

## Coding Style
## 编码风格

**Immutability (CRITICAL):** Always create new objects, never mutate. Return new copies with changes applied.
**不可变性（关键）：** 始终创建新对象，永不改变。返回应用了更改的新副本。

**File organization:** Many small files over few large ones. 200-400 lines typical, 800 max. Organize by feature/domain, not by type. High cohesion, low coupling.
**文件组织：** 多个小文件优于少数大文件。典型 200-400 行，最多 800。按特性/域组织，而非按类型。高内聚，低耦合。

**Error handling:** Handle errors at every level. Provide user-friendly messages in UI code. Log detailed context server-side. Never silently swallow errors.
**错误处理：** 每层都处理错误。UI 代码中提供用户友好的消息。服务器端记录详细上下文。永不静默吞下错误。

**Input validation:** Validate all user input at system boundaries. Use schema-based validation. Fail fast with clear messages. Never trust external data.
**输入验证：** 在系统边界验证所有用户输入。使用基于模式的验证。快速失败并提供清晰消息。永不信任外部数据。

**Code quality checklist:**
**代码质量检查清单：**

- Functions small (<50 lines), files focused (<800 lines)
- 函数小（<50 行），文件专注（<800 行）
- No deep nesting (>4 levels)
- 无深层嵌套（>4 层）
- Proper error handling, no hardcoded values
- 适当的错误处理，无硬编码值
- Readable, well-named identifiers
- 可读性，命名良好的标识符

## Testing Requirements
## 测试要求

**Minimum coverage: 80%**
**最低覆盖率：80%**

Test types (all required):
测试类型（都需要）：
1. **Unit tests** — Individual functions, utilities, components
1. **单元测试** — 单独函数、工具、组件
2. **Integration tests** — API endpoints, database operations
2. **集成测试** — API 端点、数据库操作
3. **E2E tests** — Critical user flows
3. **E2E 测试** — 关键用户流程

**TDD workflow (mandatory):**
**TDD 工作流（强制）：**
1. Write test first (RED) — test should FAIL
1. 先写测试（红色）— 测试应该失败
2. Write minimal implementation (GREEN) — test should PASS
2. 写最小实现（绿色）— 测试应该通过
3. Refactor (IMPROVE) — verify coverage 80%+
3. 重构（改进）— 验证覆盖率 80%+

Troubleshoot failures: check test isolation → verify mocks → fix implementation (not tests, unless tests are wrong).
故障排除：检查测试隔离 → 验证 mocks → 修复实现（不是测试，除非测试错误）。

## Development Workflow
## 开发工作流

1. **Plan** — Use planner agent, identify dependencies and risks, break into phases
1. **计划** — 使用 planner agent，识别依赖和风险，分成阶段
2. **TDD** — Use tdd-guide agent, write tests first, implement, refactor
2. **TDD** — 使用 tdd-guide agent，先写测试，实现，重构
3. **Review** — Use code-reviewer agent immediately, address CRITICAL/HIGH issues
3. **审查** — 立即使用 code-reviewer agent，解决关键/高优先级问题
4. **Capture knowledge in the right place**
4. **在正确的地方捕获知识**
   - Personal debugging notes, preferences, and temporary context → auto memory
   - 个人调试笔记、偏好和临时上下文 → 自动记忆
   - Team/project knowledge (architecture decisions, API changes, runbooks) → the project's existing docs structure
   - 团队/项目知识（架构决策、API 更改、运维手册）→ 项目现有文档结构
   - If the current task already produces the relevant docs or code comments, do not duplicate the same information elsewhere
   - 如果当前任务已经产生相关文档或代码注释，不要在其他地方重复相同信息
   - If there is no obvious project doc location, ask before creating a new top-level file
   - 如果没有明显的项目文档位置，创建新顶级文件前先询问
5. **Commit** — Conventional commits format, comprehensive PR summaries
5. **提交** — 常规提交格式，综合 PR 摘要

## Git Workflow
## Git 工作流

**Commit format:** `<type>: <description>` — Types: feat, fix, refactor, docs, test, chore, perf, ci
**提交格式：** `<type>: <description>` — 类型：feat, fix, refactor, docs, test, chore, perf, ci

**PR workflow:** Analyze full commit history → draft comprehensive summary → include test plan → push with `-u` flag.
**PR 工作流：** 分析完整提交历史 → 起草综合摘要 → 包含测试计划 → 使用 `-u` 标志推送。

## Architecture Patterns
## 架构模式

**API response format:** Consistent envelope with success indicator, data payload, error message, and pagination metadata.
**API 响应格式：** 带成功指示器、数据 payload、错误消息和分页元数据的一致信封。

**Repository pattern:** Encapsulate data access behind standard interface (findAll, findById, create, update, delete). Business logic depends on abstract interface, not storage mechanism.
**仓储模式：** 将数据访问封装在标准接口后面（findAll, findById, create, update, delete）。业务逻辑依赖于抽象接口，而非存储机制。

**Skeleton projects:** Search for battle-tested templates, evaluate with parallel agents (security, extensibility, relevance), clone best match, iterate within proven structure.
**骨架项目：** 搜索经过实战检验的模板，用并行 agents 评估（安全、可扩展性、相关性），克隆最佳匹配，在经过验证的结构中迭代。

## Performance
## 性能

**Context management:** Avoid last 20% of context window for large refactoring and multi-file features. Lower-sensitivity tasks (single edits, docs, simple fixes) tolerate higher utilization.
**上下文管理：** 大型重构和多文件功能避免使用最后 20% 的上下文窗口。较低敏感度任务（单独编辑、文档、简单修复）容忍更高的利用率。

**Build troubleshooting:** Use build-error-resolver agent → analyze errors → fix incrementally → verify after each fix.
**构建故障排除：** 使用 build-error-resolver agent → 分析错误 → 增量修复 → 每次修复后验证。

## Project Structure
## 项目结构

```
agents/          — 25 specialized subagents
skills/          — 102 workflow skills and domain knowledge
commands/        — 57 slash commands
hooks/           — Trigger-based automations
rules/           — Always-follow guidelines (common + per-language)
scripts/         — Cross-platform Node.js utilities
mcp-configs/     — 14 MCP server configurations
tests/           — Test suite
```
```
agents/          — 25 个专业子代理
skills/          — 102 个工作流 skills 和领域知识
commands/        — 57 个斜杠命令
hooks/           — 基于触发器的自动化
rules/           — 始终遵循的指南（通用 + 每语言）
scripts/         — 跨平台 Node.js 工具
mcp-configs/     — 14 个 MCP 服务器配置
tests/           — 测试套件
```

## Success Metrics
## 成功指标

- All tests pass with 80%+ coverage
- 所有测试通过且覆盖率 80%+
- No security vulnerabilities
- 无安全漏洞
- Code is readable and maintainable
- 代码可读且可维护
- Performance is acceptable
- 性能可接受
- User requirements are met
- 满足用户需求
