---
name: agentic-engineering
description: Operate as an agentic engineer using eval-first execution, decomposition, and cost-aware model routing.
description zh-CN: 以代理工程师的身份运作，采用 eval-first 执行、任务分解和成本感知的模型路由。
origin: ECC
---

# Agentic Engineering

# Agentic Engineering
# 代理工程

Use this skill for engineering workflows where AI agents perform most implementation work and humans enforce quality and risk controls.
当你需要 AI agent 执行大部分实现工作、人类负责质量控制和工作流程时，使用此技能。

## Operating Principles

## Operating Principles
## 运营原则

1. Define completion criteria before execution.
  在执行前定义完成标准。
2. Decompose work into agent-sized units.
  将工作分解为适合 agent 的单元。
3. Route model tiers by task complexity.
  根据任务复杂度路由到不同层级的模型。
4. Measure with evals and regression checks.
  通过评估和回归检查进行度量。

## Eval-First Loop

## Eval-First Loop
## Eval-First 循环

1. Define capability eval and regression eval.
  定义能力评估和回归评估。
2. Run baseline and capture failure signatures.
  运行基线并捕获失败特征。
3. Execute implementation.
  执行实现。
4. Re-run evals and compare deltas.
  重新运行评估并比较差异。

## Task Decomposition

## Task Decomposition
## 任务分解

Apply the 15-minute unit rule:
应用 15 分钟单元规则：
- each unit should be independently verifiable
  - 每个单元应可独立验证
- each unit should have a single dominant risk
  - 每个单元应有一个主要风险
- each unit should expose a clear done condition
  - 每个单元应暴露明确的完成条件

## Model Routing

## Model Routing
## 模型路由

- Haiku: classification, boilerplate transforms, narrow edits
  - Haiku：分类、样板转换、窄范围编辑
- Sonnet: implementation and refactors
  - Sonnet：实现和重构
- Opus: architecture, root-cause analysis, multi-file invariants
  - Opus：架构设计、根本原因分析、多文件不变量

## Session Strategy

## Session Strategy
## 会话策略

- Continue session for closely-coupled units.
  对于紧密耦合的单元继续使用同一会话。
- Start fresh session after major phase transitions.
  在主要阶段转换后开启新会话。
- Compact after milestone completion, not during active debugging.
  在里程碑完成后压缩，而非在活跃调试期间。

## Review Focus for AI-Generated Code

## Review Focus for AI-Generated Code
## AI 生成代码的审查重点

Prioritize:
优先关注：
- invariants and edge cases
  - 不变量和边界情况
- error boundaries
  - 错误边界
- security and auth assumptions
  - 安全和认证假设
- hidden coupling and rollout risk
  - 隐藏耦合和发布风险

Do not waste review cycles on style-only disagreements when automated format/lint already enforce style.
当自动格式化/lint 工具已强制执行样式时，不要在纯样式问题上浪费审查轮次。

## Cost Discipline

## Cost Discipline
## 成本纪律

Track per task:
每个任务跟踪：
- model
  - 模型
- token estimate
  - Token 估算
- retries
  - 重试次数
- wall-clock time
  - 墙上时钟时间
- success/failure
  - 成功/失败

Escalate model tier only when lower tier fails with a clear reasoning gap.
仅当下层模型因明确的推理差距失败时才升级模型层级。
