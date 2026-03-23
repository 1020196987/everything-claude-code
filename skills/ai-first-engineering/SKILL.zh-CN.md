---
name: ai-first-engineering
description: Engineering operating model for teams where AI agents generate a large share of implementation output.
description zh-CN: 为 AI agent 生成大部分实现输出的团队提供的工程运营模式。
origin: ECC
---

# AI-First Engineering

# AI-First Engineering
# AI-First 工程

Use this skill when designing process, reviews, and architecture for teams shipping with AI-assisted code generation.
当你为使用 AI 辅助代码生成的团队设计流程、审查和架构时，使用此技能。

## Process Shifts

## Process Shifts
## 流程转变

1. Planning quality matters more than typing speed.
  规划质量比打字速度更重要。
2. Eval coverage matters more than anecdotal confidence.
  评估覆盖率比主观信心更重要。
3. Review focus shifts from syntax to system behavior.
  审查重点从语法转向系统行为。

## Architecture Requirements

## Architecture Requirements
## 架构要求

Prefer architectures that are agent-friendly:
优先选择对 agent 友好的架构：
- explicit boundaries
  - 明确的边界
- stable contracts
  - 稳定的契约
- typed interfaces
  - 类型化接口
- deterministic tests
  - 确定性测试

Avoid implicit behavior spread across hidden conventions.
避免隐式行为散布在隐藏的约定中。

## Code Review in AI-First Teams

## Code Review in AI-First Teams
## AI-First 团队中的代码审查

Review for:
审查重点：
- behavior regressions
  - 行为回归
- security assumptions
  - 安全假设
- data integrity
  - 数据完整性
- failure handling
  - 失败处理
- rollout safety
  - 发布安全

Minimize time spent on style issues already covered by automation.
最小化在已被自动化覆盖的样式问题上花费的时间。

## Hiring and Evaluation Signals

## Hiring and Evaluation Signals
## 招聘和评估信号

Strong AI-first engineers:
优秀的 AI-First 工程师：
- decompose ambiguous work cleanly
  - 能清晰地分解模糊的工作
- define measurable acceptance criteria
  - 定义可衡量的验收标准
- produce high-signal prompts and evals
  - 生成高信号量的提示和评估
- enforce risk controls under delivery pressure
  - 在交付压力下执行风险控制

## Testing Standard

## Testing Standard
## 测试标准

Raise testing bar for generated code:
提高生成代码的测试标准：
- required regression coverage for touched domains
  - 对触及的领域要求回归覆盖率
- explicit edge-case assertions
  - 显式的边界情况断言
- integration checks for interface boundaries
  - 接口边界的集成检查
