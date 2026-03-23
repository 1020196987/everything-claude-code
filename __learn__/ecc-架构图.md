# ECC (Everything Claude Code) 插件加载机制与架构

> 基于 `everything-claude-code@1.8.0` 源码分析

---

## 一、整体架构图

```mermaid
flowchart TB
    subgraph USER["用户提问"]
        Q[用户输入问题]
    end

    subgraph CLAUDE["Claude Code 核心"]
        RT[运行时上下文组装]
        SKILL[Skill 匹配引擎]
        LLM[大模型 API 调用]
        TOOL[工具执行引擎]
    end

    subgraph PLUGIN["ECC 插件 (v1.8.0)"]
        subgraph RULES["rules/ — 全局规则 (alwaysApply)"]
            R_COMMON[rules/common/ — 通用规则]
            R_LANG["rules/{lang}/ — 语言特定规则"]
        end

        subgraph AGENTS["agents/ — 专家 Agent (17个)"]
            A_PLAN[planner]
            A_ARCH[architect]
            A_TDD[tdd-guide]
            A_CR[code-reviewer]
            A_SEC[security-reviewer]
            A_BUILD[build-error-resolver]
            A_E2E[e2e-runner]
            A_REFACTOR[refactor-cleaner]
            A_DOC[doc-updater]
            A_GO[go-reviewer / go-build-resolver]
            A_PY[python-reviewer]
            A_KT[kotlin-reviewer / kotlin-build-resolver]
            A_DB[database-reviewer]
            A_COS[chief-of-staff]
            A_LOOP[loop-operator]
            A_HARNESS[harness-optimizer]
        end

        subgraph CMDS["commands/ — 斜杠命令 (40+个)"]
            CMD_PLAN["/plan"]
            CMD_TDD["/tdd"]
            CMD_CR["/code-review"]
            CMD_ORCH["/orchestrate"]
            CMD_MULTI["/multi-workflow"]
            CMD_LEARN["/learn"]
            CMD_SKILL["/skill-create"]
            CMD_INSTINCT["/instinct-*"]
            CMD_OTHERS[...]
        end

        subgraph SKILLS["skills/ — 领域技能 (96个)"]
            SK_WORKFLOW[workflow-quality]
            SK_FRAME[framework-language]
            SK_DB[database]
            SK_SEC[security]
            SK_RESEARCH[research-apis]
            SK_BUSINESS[business-content]
            SK_MEDIA[media-generation]
            SK_ORCH[orchestration]
        end

        subgraph HOOKS["hooks/ — 生命周期钩子"]
            H_SESSIONSTART[SessionStart]
            H_SESSIONEND[SessionEnd]
            H_PRETOOL[PreToolUse]
            H_POSTTOOL[PostToolUse]
            H_PRECOMPACT[PreCompact]
            H_STOP[Stop]
        end

        HOOK_RT[rules-core · agents-core · commands-core · hooks-runtime · platform-configs]
    end

    subgraph HOOKS_RT["Hook 执行运行时"]
        RWF[run-with-flags.js — 核心调度器]
        H_PROF[Hook Profile: minimal / standard / strict]
        H_DISABLED[ECC_DISABLED_HOOKS 环境变量]
    end

    subgraph INSTALL["模块化安装系统"]
        IM[install-modules.json — 14个模块]
        IP[install-profiles.json — 5个 profiles]
        IC[install-components.json — 组件清单]
    end

    Q --> RT
    RT --> SKILL
    SKILL --> |"匹配触发"| LLM
    LLM --> TOOL
    TOOL --> |"触发钩子"| HOOKS

    RT --> RULES
    RT --> AGENTS
    RT --> CMDS
    RT --> SKILLS

    HOOKS --> H_SESSIONSTART
    HOOKS --> H_SESSIONEND
    HOOKS --> H_PRETOOL
    HOOKS --> H_POSTTOOL
    HOOKS --> H_PRECOMPACT
    HOOKS --> H_STOP

    H_SESSIONSTART --> RWF
    H_SESSIONEND --> RWF
    H_PRETOOL --> RWF
    H_POSTTOOL --> RWF
    H_PRECOMPACT --> RWF
    H_STOP --> RWF

    RWF --> H_PROF
    RWF --> H_DISABLED
    RWF --> HOOK_RT

    INSTALL --> |"defaultInstall: true"| HOOK_RT
```

---

## 二、上下文组装层次 (Session Start 流程)

```mermaid
sequenceDiagram
    participant U as 用户
    participant CC as Claude Code
    participant HOOK as SessionStart Hook
    participant RWF as run-with-flags.js
    participant SS as session-start.js
    participant LLM as 大模型

    Note over CC: Claude Code 启动，检测当前项目

    CC->>HOOK: 触发 SessionStart 事件
    HOOK->>RWF: 调用 hook 命令
    RWF->>RWF: 检查 ECC_HOOK_PROFILE<br/>ECC_DISABLED_HOOKS

    alt Hook 未被禁用
        RWF->>SS: 执行 session-start.js
        SS->>SS: 1. 加载最近会话摘要<br/>   (~/.claude/sessions/*-session.tmp)
        SS->>SS: 2. 检测包管理器 (npm/pnpm/yarn/bun)
        SS->>SS: 3. 检测项目类型和框架
        SS->>SS: 4. 检查 learned skills
        SS->>SS: 5. 检查 session aliases
        SS-->>CC: 输出 system-reminder 到上下文
    end

    CC->>CC: 组装完整上下文

    rect rgb(240, 248, 255)
        Note over CC: 上下文层次 (优先级从低到高)
        CC->>CC: 1. ~/.claude/CLAUDE.md (全局指令)
        CC->>CC: 2. ~/.claude/rules/*.md (全部规则文件)
        CC->>CC: 3. ~/.claude/rules/hooks.md (TypeScript Hook 配置)
        CC->>CC: 4. 项目 .claude/ (项目级配置)
        CC->>CC: 5. 插件 Skills 索引 (按需加载)
        CC->>CC: 6. 插件 Agents 索引
        CC->>CC: 7. 插件 Commands 索引
        CC->>CC: 8. 项目 .claude/homunculus/ (项目专属配置)
        CC->>CC: 9. 当前会话 system-reminder
        CC->>CC: 10. SessionStart Hook 输出 (会话摘要等)
    end

    CC->>LLM: 发送完整上下文 + 用户问题
    LLM-->>U: 生成回复
```

---

## 三、Skill 匹配与触发机制

```mermaid
flowchart TD
    Q[用户问题]
    SKILL_INDEX[Skill 索引表]
    MATCH[Skill 描述匹配检查]
    DECISION{是否有匹配?}

    Q --> SKILL_INDEX
    SKILL_INDEX --> MATCH

    MATCH --> DECISION

    DECISION --> |"匹配"| INVOKE[Skill 工具调用]
    DECISION --> |"无匹配"| DIRECT[直接响应]

    INVOKE --> LOAD[加载 SKILL.md 全文]
    LOAD --> FOLLOW[严格遵循 Skill 指令]
    FOLLOW --> HAS_CHECK{是否有 checklist?}
    HAS_CHECK --> |"是"| TODO[创建 TodoWrite 任务]
    HAS_CHECK --> |"否"| EXEC[直接执行]
    TODO --> EXEC

    subgraph SKILL_TREE["ECC Skills 分类 (96个)"]
        direction LR
        subgraph WG1["workflow-quality (7个, 默认安装)"]
            W1[tdd-workflow]
            W2[verification-loop]
            W3[eval-harness]
            W4[strategic-compact]
            W5[continuous-learning]
            W6[continuous-learning-v2]
            W7[iterative-retrieval]
        end
        subgraph WG2["framework-language (17个, 可选)"]
            F1[backend-patterns]
            F2[frontend-patterns]
            F3[coding-standards]
            F4[golang-patterns]
            F5[python-patterns]
            F6[django-patterns]
            F7[springboot-patterns]
            F8[java-coding-standards]
        end
        subgraph WG3["database (3个, 可选)"]
            DB1[postgres-patterns]
            DB2[clickhouse-io]
            DB3[jpa-patterns]
        end
        subgraph WG4["security (4个, 可选)"]
            S1[security-review]
            S2[django-security]
            S3[springboot-security]
            S4[security-scan]
        end
        subgraph WG5["research-apis (3个, 可选)"]
            R1[deep-research]
            R2[exa-search]
            R3[claude-api]
        end
        subgraph WG6["business-content (5个, 可选)"]
            B1[investor-materials]
            B2[investor-outreach]
            B3[market-research]
            B4[article-writing]
            B5[content-engine]
        end
        subgraph WG7["其他"]
            M1[media-generation: fal-ai-media, video-editing, videodb]
            OR1[orchestration: dmux-workflows, autonomous-loops]
            OTH1[crosspost, x-api, nanoclaw-repl]
        end
    end

    INVOKE -.-> SKILL_TREE
```

---

## 四、Hook 执行机制详解

```mermaid
flowchart LR
    subgraph HOOK_EVENTS["6 种 Hook 事件类型"]
        HE1[SessionStart — 会话启动]
        HE2[SessionEnd — 会话结束]
        HE3[PreToolUse — 工具执行前]
        HE4[PostToolUse — 工具执行后]
        HE5[PreCompact — 上下文压缩前]
        HE6[Stop — 每次回复后]
    end

    subgraph HOOK_DEFS["hooks.json 中的 Hook 定义"]
        HD1["PreToolUse: Bash/Write/Edit → 格式化和类型检查"]
        HD2["PostToolUse: Edit → format / typecheck / console-warn"]
        HD3["PostToolUse: Bash → PR创建日志 / 构建完成"]
        HD4["SessionStart → 加载会话摘要 / 检测包管理器"]
        HD5["Stop → console.log审计 / 会话持久化 / 成本跟踪"]
        HD6["PreCompact → 保存状态"]
    end

    subgraph RUNNER["run-with-flags.js 调度器"]
        RF1[读取 ECC_HOOK_PROFILE<br/>minimal / standard / strict]
        RF2[读取 ECC_DISABLED_HOOKS<br/>环境变量黑名单]
        RF3[根据 profile + 黑名单<br/>决定是否执行]
        RF4["直接 require() 优化<br/>(~50-100ms 节省)"]
    end

    HE1 --> HD1
    HE2 --> HD2
    HE3 --> HD3
    HE4 --> HD4
    HE5 --> HD5
    HE6 --> HD6

    HD1 --> RF1
    HD2 --> RF2
    HD3 --> RF3
    HD4 --> RF4

    RF1 --> RF3
    RF2 --> RF3
    RF3 --> |"通过"| EXEC[执行实际 hook 脚本]
    RF3 --> |"禁用"| PASS[透传 stdin 直接返回]
    EXEC --> OUT[stdout 注入上下文]
```

---

## 五、模块化安装系统

```mermaid
flowchart TB
    subgraph MANIFESTS["manifests/"]
        IM[install-modules.json<br/>14 个模块定义]
        IP[install-profiles.json<br/>5 个安装 profiles]
        IC[install-components.json<br/>组件清单]
    end

    subgraph PROFILES["5 种安装 Profile"]
        P_CORE[core — 最小基线<br/>rules + agents + commands + hooks + platform]
        P_DEV[developer — 默认工程配置<br/>core + workflow-quality]
        P_SEC[security — 安全优先<br/>developer + security]
        P_RES[research — 研究/内容导向<br/>core + workflow-quality + research-apis + business-content]
        P_FULL[full — 完整安装<br/>全部模块]
    end

    subgraph MODULES["14 个可安装模块"]
        M1[rules-core — 规则文件]
        M2[agents-core — Agent 定义]
        M3[commands-core — 斜杠命令]
        M4[hooks-runtime — Hook 运行时]
        M5[platform-configs — 平台配置]
        M6[workflow-quality — TDD/验证技能]
        M7[framework-language — 框架/语言技能]
        M8[database — 数据库技能]
        M9[security — 安全审查技能]
        M10[research-apis — 研究/API技能]
        M11[business-content — 商业/写作技能]
        M12[social-distribution — 社交发布技能]
        M13[media-generation — 媒体生成技能]
        M14[orchestration — 编排系统]
    end

    IM --> M1
    IM --> M2
    IM --> M3
    IM --> M4
    IM --> M5
    IM --> M6
    IM --> M7
    IM --> M8
    IM --> M9
    IM --> M10
    IM --> M11
    IM --> M12
    IM --> M13
    IM --> M14

    IP --> P_CORE
    IP --> P_DEV
    IP --> P_SEC
    IP --> P_RES
    IP --> P_FULL

    P_CORE --> |"依赖"| M1 & M2 & M3 & M4 & M5
    P_DEV --> P_CORE
    P_DEV --> M6
    P_SEC --> P_DEV
    P_SEC --> M9
    P_RES --> P_CORE
    P_RES --> M6 & M10 & M11
    P_FULL --> P_RES
    P_FULL --> M7 & M8 & M12 & M13 & M14

    M7 -.-> |"依赖"| M1 & M2 & M3 & M5
    M9 -.-> |"依赖"| M6
    M11 -.-> |"依赖"| M5
    M12 -.-> |"依赖"| M11
    M14 -.-> |"依赖"| M3 & M5
```

---

## 六、多平台支持架构

```mermaid
flowchart TB
    subgraph PLUGIN["ECC 插件核心 (跨平台复用)"]
        CORE_AGENTS[agents/ — 17 个 Agent 定义]
        CORE_CMDS[commands/ — 40+ 个命令]
        CORE_SKILLS[skills/ — 96 个 Skill]
        CORE_RULES[rules/ — 通用 + 语言特定规则]
        CORE_MANIFESTS[manifests/ — 模块化安装]
    end

    subgraph CLAUDE_CODE["Claude Code"]
        CC_HOOKS[hooks/hooks.json<br/>6 种事件类型]
        CC_PLATFORM[platform-configs]
    end

    subgraph CURSOR["Cursor IDE"]
        CUR_HOOKS[.cursor/hooks.json<br/>16 种事件类型<br/>含 subagent/tab/MCP 专属事件]
        CUR_SKILLS[.cursor/skills/ — 6 个 Cursor 专属]
        CUR_RULES[.cursor/rules/ — Cursor 版规则]
    end

    subgraph OPENCODE["OpenCode CLI"]
        OC_CONFIG[.opencode/opencode.json<br/>12 个 Agent / 24+ 命令]
        OC_HOOKS[.opencode/plugins/ecc-hooks.ts<br/>TypeScript Hook 插件]
    end

    subgraph CODEX["Codex CLI"]
        CX_AGENTS[.codex/AGENTS.md<br/>TOML 配置]
        CX_SKILLS[.codex/skills/ — 独立目录结构]
    end

    CORE_AGENTS --> CC_PLATFORM
    CORE_AGENTS --> OC_CONFIG
    CORE_AGENTS --> CX_AGENTS
    CORE_CMDS --> CC_PLATFORM
    CORE_CMDS --> OC_CONFIG
    CORE_SKILLS --> CC_PLATFORM
    CORE_SKILLS --> OC_CONFIG
    CORE_RULES --> CC_PLATFORM
    CORE_RULES --> CUR_RULES
    CORE_MANIFESTS --> CC_PLATFORM

    CC_HOOKS -.-> |"平台专属"| PLUGIN
    CUR_HOOKS -.-> |"平台专属"| PLUGIN
    OC_HOOKS -.-> |"平台专属"| PLUGIN
```

---

## 七、完整请求处理流程

```mermaid
sequenceDiagram
    participant U as 用户
    participant CC as Claude Code
    participant HOOK as SessionStart Hook
    participant CTX as 上下文组装
    participant SK as Skill 匹配引擎
    participant LLM as 大模型 (MiniMax)
    participant TOOL as 工具执行器
    participant H_PRE as PreToolUse Hook
    participant H_POST as PostToolUse Hook
    participant H_STOP as Stop Hook

    Note over U, LLM: 阶段 1: 会话初始化

    CC->>HOOK: SessionStart 事件
    HOOK->>HOOK: run-with-flags.js 检查 profile
    HOOK->>HOOK: session-start.js 执行:<br/>加载会话摘要 + 检测包管理器 + 检测项目类型
    HOOK-->>CC: system-reminder (会话摘要 / 项目类型)
    CC->>CTX: 组装完整上下文

    rect rgb(240, 255, 240)
        Note over CTX: 上下文组装 (优先级从低到高)
        CTX->>CTX: 1. ~/.claude/CLAUDE.md
        CTX->>CTX: 2. ~/.claude/rules/*.md (全量加载)
        CTX->>CTX: 3. 插件 Skills 索引 (所有 SKILL.md 的 name/description)
        CTX->>CTX: 4. 插件 Agents 索引 (所有 agent 的 name/description)
        CTX->>CTX: 5. 插件 Commands 索引 (所有 command 的 description)
        CTX->>CTX: 6. 项目级 .claude/ 配置
        CTX->>CTX: 7. system-reminder 注入
    end

    Note over U, LLM: 阶段 2: 问题处理

    U->>CC: 用户问题
    CC->>SK: 扫描所有 Skill 的 description 触发条件

    alt Skill 匹配成功
        SK->>SK: 调用 Skill 工具
        SK->>SK: 加载 SKILL.md 全文
        SK->>SK: 强制遵循 Skill 指令流程
    end

    CC->>LLM: 发送 (上下文 + Skill 内容 + 用户问题)

    Note over U, LLM: 阶段 3: 工具执行循环

    LLM->>TOOL: 返回工具调用请求
    TOOL->>H_PRE: PreToolUse 钩子触发
    H_PRE->>H_PRE: run-with-flags.js 过滤
    H_PRE->>H_PRE: 例如: auto-tmux-dev / format / typecheck
    H_PRE-->>TOOL: 继续执行

    TOOL->>TOOL: 执行工具 (Read/Write/Edit/Bash/Grep...)

    TOOL->>H_POST: PostToolUse 钩子触发
    H_POST->>H_POST: run-with-flags.js 过滤
    H_POST->>H_POST: 例如: 格式检查 / 类型检查 / console-warn
    H_POST-->>TOOL: 工具结果返回

    TOOL-->>LLM: 工具结果
    LLM-->>CC: 最终回复

    Note over U, LLM: 阶段 4: 停止钩子

    CC->>H_STOP: Stop 钩子触发
    H_STOP->>H_STOP: console.log 审计
    H_STOP->>H_STOP: 会话状态持久化
    H_STOP->>H_STOP: 成本跟踪
    H_STOP-->>CC: 完成

    CC-->>U: 显示回复
```

---

## 八、核心文件速查表

| 功能 | 文件路径 |
|------|----------|
| 插件清单 | `.claude-plugin/plugin.json` |
| 安装模块定义 | `manifests/install-modules.json` |
| 安装 profiles | `manifests/install-profiles.json` |
| Claude Code Hooks 配置 | `hooks/hooks.json` |
| Hook 核心调度器 | `scripts/hooks/run-with-flags.js` |
| SessionStart 脚本 | `scripts/hooks/session-start.js` |
| SessionEnd 脚本 | `scripts/hooks/session-end.js` |
| Hook Profile 控制 | 读取 `ECC_HOOK_PROFILE` (minimal/standard/strict) |
| Hook 黑名单 | `ECC_DISABLED_HOOKS` 环境变量 |
| Agent 定义示例 | `agents/planner.md` |
| Command 定义示例 | `commands/plan.md` |
| Skill 定义示例 | `skills/tdd-workflow/SKILL.md` |
| 通用规则 | `rules/common/*.md` |
| 语言特定规则 | `rules/{lang}/*.md` |
| OpenCode 配置 | `.opencode/opencode.json` |
| Cursor Hooks | `.cursor/hooks.json` |
| MCP 服务器配置 | `mcp-configs/mcp-servers.json` |
