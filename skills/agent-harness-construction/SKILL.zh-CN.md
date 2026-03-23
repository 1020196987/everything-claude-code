---
name: agent-harness-construction
description: Design and optimize AI agent action spaces, tool definitions, and observation formatting for higher completion rates.
description zh-CN: 设计和优化 AI agent 的动作空间、工具定义和观察格式，以提高完成率。
origin: ECC
---

# Agent Harness Construction

# Agent 工具架构建

Use this skill when you are improving how an agent plans, calls tools, recovers from errors, and converges on completion.
当你正在改进 agent 的规划、调用工具、从错误恢复和收敛到完成的机制时，使用此技能。

## Core Model

## Core Model
## 核心模型

Agent output quality is constrained by:
Agent 输出质量受以下因素约束：
1. Action space quality
  - 动作空间质量
2. Observation quality
  - 观察质量
3. Recovery quality
  - 恢复质量
4. Context budget quality
  - 上下文预算质量

## Action Space Design

## Action Space Design
## 动作空间设计

1. Use stable, explicit tool names.
  使用稳定、明确的工具名称。
2. Keep inputs schema-first and narrow.
  保持输入的 schema-first（模式优先）和窄化。
3. Return deterministic output shapes.
  返回确定性的输出结构。
4. Avoid catch-all tools unless isolation is impossible.
  避免使用"万能"工具，除非隔离是不可能的。

## Granularity Rules

## Granularity Rules
## 粒度规则

- Use micro-tools for high-risk operations (deploy, migration, permissions).
  对高风险操作（部署、迁移、权限）使用微工具。
- Use medium tools for common edit/read/search loops.
  对常见的编辑/读取/搜索循环使用中等粒度工具。
- Use macro-tools only when round-trip overhead is the dominant cost.
  只有当往返开销是主要成本时才使用宏工具。

## Observation Design

## Observation Design
## 观察设计

Every tool response should include:
每个工具响应应包含：
- `status`: success|warning|error
  - `status`: success|warning|error
- `summary`: one-line result
  - `summary`: 一行结果摘要
- `next_actions`: actionable follow-ups
  - `next_actions`: 可操作的跟进步骤
- `artifacts`: file paths / IDs
  - `artifacts`: 文件路径/ID

## Error Recovery Contract

## Error Recovery Contract
## 错误恢复契约

For every error path, include:
对于每个错误路径，包含：
- root cause hint
  - 根本原因提示
- safe retry instruction
  - 安全重试指令
- explicit stop condition
  - 明确的停止条件

## Context Budgeting

## Context Budgeting
## 上下文预算管理

1. Keep system prompt minimal and invariant.
  保持系统提示简洁且不变。
2. Move large guidance into skills loaded on demand.
  将大型指导内容移至按需加载的技能中。
3. Prefer references to files over inlining long documents.
  优先引用文件而非内联长文档。
4. Compact at phase boundaries, not arbitrary token thresholds.
  在阶段边界压缩，而非在任意 token 阈值处压缩。

## Architecture Pattern Guidance

## Architecture Pattern Guidance
## 架构模式指导

- ReAct: best for exploratory tasks with uncertain path.
  - ReAct：最适合路径不确定的探索性任务。
- Function-calling: best for structured deterministic flows.
  - Function-calling：最适合结构化的确定性流程。
- Hybrid (recommended): ReAct planning + typed tool execution.
  - 混合（推荐）：ReAct 规划 + 类型化工具执行。

## Benchmarking

## Benchmarking
## 基准测试

Track:
跟踪：
- completion rate
  - 完成率
- retries per task
  - 每个任务的平均重试次数
- pass@1 and pass@3
  - pass@1 和 pass@3
- cost per successful task
  - 每个成功任务的成本

## Anti-Patterns

## Anti-Patterns
## 反模式

- Too many tools with overlapping semantics.
  - 语义重叠的工具过多。
- Opaque tool output with no recovery hints.
  - 工具输出不透明，缺乏恢复提示。
- Error-only output without next steps.
  - 仅输出错误，没有后续步骤。
- Context overloading with irrelevant references.
  - 上下文被无关引用过载。
