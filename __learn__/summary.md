# Everything Claude Code 项目分析报告

## 1. 项目整体定位

### 1.1 核心用途

Everything Claude Code (ECC) 是一个面向 AI 编程代理(Agent Harness)的性能优化系统，由 Anthropic Hackathon 获奖者开发。该项目不仅仅是一组配置文件，而是一个完整的系统，包含：

- **技能(Skills)**: 领域特定的工作流定义和知识
- **记忆(Memory)**: 跨会话的上下文持久化
- **性能优化**: Token 优化、内存管理
- **持续学习**: 从会话中自动提取可复用模式
- **安全扫描**: 代码安全检查
- **研究优先开发**: 研究导向的开发模式

### 1.2 主要功能

| 功能类别 | 描述 |
|---------|------|
| 代理(Agents) | 13个专业化子代理用于委托任务 |
| 技能(Skills) | 70+ 领域特定技能 |
| 命令(Commands) | 42+ 用户可调用命令 |
| 钩子(Hooks) | 30+ 自动化工作流 |
| 规则(Rules) | 多语言编码规范和安全准则 |
| MCP配置 | 外部服务集成配置 |

### 1.3 面向用户群体

- **AI 开发者**: 使用 Claude Code、Codex、Cursor、OpenCode 等 AI 编程助手的开发者
- **软件团队**: 需要标准化开发流程的团队
- **个人开发者**: 希望提升 AI 编程效率的程序员

---

## 2. 技术栈分析

### 2.1 编程语言

| 语言 | 用途 | 占比 |
|------|------|------|
| JavaScript | 核心脚本、Hooks、工具函数 | 主要 |
| TypeScript | OpenCode 插件开发 | 部分 |
| Shell | 安装脚本、CI/CD | 部分 |
| Markdown | 文档、配置、Agent/Skill 定义 | 大量 |

### 2.2 关键依赖库

```json
{
  "devDependencies": {
    "@eslint/js": "^9.39.2",
    "eslint": "^9.39.2",
    "globals": "^17.1.0",
    "markdownlint-cli": "^0.47.0"
  }
}
```

### 2.3 运行环境和平台支持

- **Node.js**: >= 18
- **跨平台**: Windows、macOS、Linux
- **AI Harness 兼容**: Claude Code, Codex, Cursor, OpenCode

---

## 3. 目录结构说明

### 3.1 一级目录概览

| 目录 | 职责 | 文件数 |
|------|------|--------|
| `agents/` | 子代理定义 | 16 |
| `commands/` | 用户可调用命令 | 42 |
| `skills/` | 领域技能定义 | 70+ |
| `hooks/` | 触发式自动化 | 2 |
| `rules/` | 编码规范和安全准则 | 30+ |
| `scripts/` | 工具脚本和库 | 50+ |
| `tests/` | 测试套件 | 10+ |
| `mcp-configs/` | MCP 服务器配置 | 1 |
| `contexts/` | 开发上下文定义 | 3 |
| `examples/` | 示例项目 | 7 |
| `docs/` | 多语言文档 | 100+ |
| `schemas/` | JSON Schema 定义 | 3 |
| `assets/` | 静态资源 | - |
| `plugins/` | 插件配置 | - |
| `.claude/` | Claude Code 配置 | - |
| `.claude-plugin/` | 插件元数据 | - |
| `.opencode/` | OpenCode 集成 | - |
| `.cursor/` | Cursor IDE 配置 | - |
| `.codex/` | Codex 配置 | - |

---

## 4. 根目录文件功能梳理

### 4.1 项目配置文件

| 文件名 | 类型 | 核心功能 | 关键内容 |
|--------|------|----------|----------|
| `package.json` | JSON | npm 包配置，定义项目元数据和脚本 | name: ecc-universal, bin: ecc-install |
| `package-lock.json` | JSON | 依赖版本锁定文件 | 依赖树精确版本 |
| `.gitignore` | 文本 | Git 忽略规则 | node_modules, .git 等 |
| `.npmignore` | 文本 | npm 发布忽略文件 | 测试文件等 |
| `.prettierrc` | JSON | Prettier 代码格式化配置 | 缩进、引号规则 |
| `.markdownlint.json` | JSON | Markdown lint 配置 | 行长度、列表规则 |
| `eslint.config.js` | JS | ESLint JavaScript 检查配置 | 规则集定义 |
| `commitlint.config.js` | JS | Git 提交信息规范 | conventional commits |

### 4.2 文档文件

| 文件名 | 类型 | 核心功能 | 关键内容 |
|--------|------|----------|----------|
| `README.md` | Markdown | 项目主文档（英文） | 53KB，详细介绍 |
| `README.zh-CN.md` | Markdown | 中文文档 | 17KB |
| `CLAUDE.md` | Markdown | Claude Code 项目指南 | 2.4KB |
| `AGENTS.md` | Markdown | Agent 指令索引 | 6.5KB |
| `CHANGELOG.md` | Markdown | 版本变更日志 | 1.5KB |
| `CONTRIBUTING.md` | Markdown | 贡献指南 | 8.4KB |
| `CODE_OF_CONDUCT.md` | Markdown | 行为准则 | 5.2KB |
| `LICENSE` | 文本 | MIT 许可证 | 1.1KB |
| `SPONSORING.md` | Markdown | 赞助信息 | 1.8KB |
| `SPONSORS.md` | Markdown | 赞助者名单 | 1.9KB |
| `the-shortform-guide.md` | Markdown | 快速入门指南 | 16KB |
| `the-longform-guide.md` | Markdown | 详细使用指南 | 43KB |
| `the-security-guide.md` | Markdown | 安全指南 | 29KB |
| `the-openclaw-guide.md` | Markdown | OpenClaw 指南 | 43KB |

### 4.3 安装和脚本文件

| 文件名 | 类型 | 核心功能 | 关键内容 |
|--------|------|----------|----------|
| `install.sh` | Shell | 安装脚本 | 8.4KB, 支持多平台安装 |

---

## 5. 核心模块分析

### 5.1 Agents 模块

**职责**: 提供专业化的子代理，用于委托特定领域任务

**包含代理**:

| 代理名称 | 功能 | 使用场景 |
|---------|------|----------|
| `planner` | 实现规划 | 复杂功能、重构 |
| `architect` | 系统设计 | 架构决策 |
| `tdd-guide` | 测试驱动开发 | 新功能、bug 修复 |
| `code-reviewer` | 代码质量审查 | 代码编写后 |
| `security-reviewer` | 安全漏洞检测 | 提交前、敏感代码 |
| `build-error-resolver` | 构建错误修复 | 构建失败时 |
| `e2e-runner` | 端到端测试 | 关键用户流程 |
| `refactor-cleaner` | 死代码清理 | 代码维护 |
| `doc-updater` | 文档更新 | 文档同步 |
| `go-reviewer` | Go 代码审查 | Go 项目 |
| `go-build-resolver` | Go 构建错误 | Go 构建失败 |
| `python-reviewer` | Python 代码审查 | Python 项目 |
| `database-reviewer` | PostgreSQL/Supabase 专家 | 模式设计、查询优化 |

**交互方式**: 用户通过 `/agent-name` 命令调用，或在代码中引用

### 5.2 Skills 模块

**职责**: 领域特定的工作流定义和知识库

**技能分类**:

| 类别 | 示例技能 |
|------|----------|
| **编程范式** | tdd-workflow, coding-standards, python-patterns, golang-patterns |
| **前端** | frontend-patterns, frontend-slides, liquid-glass-design |
| **后端** | backend-patterns, api-design, deployment-patterns |
| **数据库** | postgres-patterns, clickhouse-io, database-migrations |
| **框架特定** | django-patterns, springboot-patterns, swiftui-patterns |
| **安全** | security-review, security-scan, django-security |
| **测试** | e2e-testing, python-testing, golang-testing |
| **DevOps** | docker-patterns, deployment-patterns |
| **学习系统** | continuous-learning-v2, eval-harness, verification-loop |
| **商业/内容** | article-writing, content-engine, market-research, investor-materials |

**交互方式**: 通过 Skill 工具调用

### 5.3 Commands 模块

**职责**: 用户可调用的斜杠命令

| 命令类别 | 示例命令 |
|----------|----------|
| **开发流程** | /tdd, /plan, /e2e, /verify |
| **代码质量** | /code-review, /build-fix, /python-review |
| **学习** | /learn, /learn-eval, /skill-create |
| **代理管理** | /multi-plan, /multi-execute, /pm2 |
| **运维** | /harness-audit, /loop-start, /quality-gate |

### 5.4 Hooks 模块

**职责**: 基于事件触发的自动化工作流

**Hook 类型**:

| 阶段 | 功能 | 数量 |
|------|------|------|
| `PreToolUse` | 工具执行前 | 6 |
| `PostToolUse` | 工具执行后 | 6 |
| `PreCompact` | 上下文压缩前 | 1 |
| `SessionStart` | 会话开始 | 1 |
| `SessionEnd` | 会话结束 | 1 |
| `Stop` | 响应后停止前 | 4 |

**关键 Hook 功能**:
- 自动启动开发服务器 (tmux)
- Git push 前提醒
- 代码格式化 (Prettier/Biome)
- TypeScript 类型检查
- 会话状态持久化
- 成本追踪
- 质量门禁检查

### 5.5 Rules 模块

**职责**: 编码规范、安全准则、开发流程

**规则结构**:
```
rules/
├── common/       # 通用规则
│   ├── agents.md
│   ├── coding-style.md
│   ├── development-workflow.md
│   ├── git-workflow.md
│   ├── hooks.md
│   ├── patterns.md
│   ├── performance.md
│   ├── security.md
│   └── testing.md
├── golang/       # Go 语言规则
├── python/       # Python 规则
├── swift/        # Swift 规则
└── typescript/   # TypeScript 规则
```

### 5.6 Scripts 模块

**职责**: 工具脚本和库函数

**子目录**:

| 子目录 | 功能 |
|--------|------|
| `ci/` | CI/CD 验证脚本 |
| `hooks/` | Hook 运行时脚本 |
| `lib/` | 共享工具库 |
| `codemaps/` | 代码地图生成 |

**关键库函数** (`lib/`):

| 文件 | 功能 |
|------|------|
| `package-manager.js` | 包管理器检测 (npm/pnpm/yarn/bun) |
| `utils.js` | 通用工具函数 |
| `session-manager.js` | 会话管理 |
| `session-aliases.js` | 会话别名 |
| `project-detect.js` | 项目类型检测 |
| `hook-flags.js` | Hook 标志处理 |

---

## 6. 入口文件和启动流程

### 6.1 入口点分析

**主要入口**:

| 入口 | 描述 |
|------|------|
| `package.json` | npm 包入口，定义 bin 命令 |
| `install.sh` | 插件安装脚本 |
| `hooks/hooks.json` | Hook 系统配置入口 |

### 6.2 安装和启动流程

```bash
# 方式1: npm 全局安装
npm install -g ecc-universal

# 方式2: npx 运行
npx ecc-install

# 安装后可通过 / 命令调用各种命令
/tdd           # TDD 工作流
/plan          # 规划功能
/code-review   # 代码审查
```

### 6.3 运行测试

```bash
# 运行所有测试
node tests/run-all.js

# 运行单个测试
node tests/lib/utils.test.js
node tests/lib/package-manager.test.js
node tests/hooks/hooks.test.js
```

### 6.4 开发工作流

1. **克隆仓库**: `git clone https://github.com/affaan-m/everything-claude-code`
2. **安装依赖**: `npm install`
3. **运行测试**: `npm test`
4. **本地开发**: 修改 agents/skills/commands/hooks/rules
5. **提交贡献**: 按 CONTRIBUTING.md 规范

---

## 7. 关键概念解释

| 概念 | 解释 |
|------|------|
| **Agent (代理)** | 可被委托执行特定任务的 AI 子程序 |
| **Skill (技能)** | 领域特定的工作流定义和知识库 |
| **Hook (钩子)** | 基于事件触发的自动化脚本 |
| **Instinct (本能)** | 从会话中学习的可复用模式 |
| **Harness (代理框架)** | AI 编程助手的运行时环境 |
| **Continuous Learning (持续学习)** | 自动从会话中提取模式并保存 |
| **Quality Gate (质量门禁)** | 代码提交前的质量检查点 |
| **Token Optimization** | 降低 AI 交互成本的技术 |

---

## 8. 文件清单汇总表

### 配置文件

| 文件 | 关联 |
|------|------|
| package.json | 所有模块入口 |
| eslint.config.js | scripts/lib/* |
| commitlint.config.js | Git hooks |

### 文档文件

| 文件 | 关联 |
|------|------|
| README.md | 项目首页 |
| CLAUDE.md | agents/, commands/, skills/ |
| CONTRIBUTING.md | 所有模块 |

### 核心模块

| 模块 | 入口文件 | 依赖 |
|------|----------|------|
| agents/ | agents/*.md | hooks, rules |
| commands/ | commands/*.md | agents, skills |
| skills/ | skills/*/SKILL.md | agents |
| hooks/ | hooks/hooks.json | scripts/hooks/ |
| rules/ | rules/*/*.md | - |
| scripts/ | scripts/*.js | lib/* |

---

*报告生成时间: 2026-03-10*
*项目版本: 1.8.0*
