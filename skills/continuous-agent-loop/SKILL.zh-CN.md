---
name: continuous-agent-loop
description: Patterns for continuous autonomous agent loops with quality gates, evals, and recovery controls.
description zh-CN: 带有质量门、评估和恢复控制的连续自主代理循环模式。
origin: ECC
---

# Continuous Agent Loop
# Continuous Agent Loop
# 连续代理循环

This is the v1.8+ canonical loop skill name. It supersedes `autonomous-loops` while keeping compatibility for one release.
## This is the v1.8+ canonical loop skill name. It supersedes `autonomous-loops` while keeping compatibility for one release.
## 这是 v1.8+ 的规范循环技能名称。它取代了 `autonomous-loops`，同时保持一个版本的兼容性。

## Loop Selection Flow
## Loop Selection Flow
## 循环选择流程

```text
Start
  |
  +-- Need strict CI/PR control? -- yes --> continuous-pr
  +-- Need RFC decomposition? -- yes --> rfc-dag
  +-- Need exploratory parallel generation? -- yes --> infinite
  +-- default --> sequential
```

## Combined Pattern
## Combined Pattern
## 组合模式

Recommended production stack:
## Recommended production stack:
## 推荐的生产技术栈：

1. RFC decomposition (`ralphinho-rfc-pipeline`)
2. quality gates (`plankton-code-quality` + `/quality-gate`)
3. eval loop (`eval-harness`)
4. session persistence (`nanoclaw-repl`)

## Failure Modes
## Failure Modes
## 失败模式

- loop churn without measurable progress
  - 循环空转但无明显进展
- repeated retries with same root cause
  - 反复重试但根因相同
- merge queue stalls
  - 合并队列停滞
- cost drift from unbounded escalation
  - 由于无限升级导致成本漂移

## Recovery
## Recovery
## 恢复

- freeze loop
  - 冻结循环
- run `/harness-audit`
  - 运行 `/harness-audit`
- reduce scope to failing unit
  - 将范围缩小到失败的单元
- replay with explicit acceptance criteria
  - 使用明确的验收标准重放
