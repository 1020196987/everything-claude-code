---
name: ralphinho-rfc-pipeline
description: RFC-driven multi-agent DAG execution pattern with quality gates, merge queues, and work unit orchestration.
description zh-CN: RFC驱动的多Agent DAG执行模式，带有质量门、合并队列和工作单元编排。
origin: ECC
---

# Ralphinho RFC Pipeline
# Ralphinho RFC 流水线

Inspired by [humanplane](https://github.com/humanplane) style RFC decomposition patterns and multi-unit orchestration workflows.
灵感来自 [humanplane](https://github.com/humanplane) 风格的 RFC 分解模式和多单元编排工作流。

Use this skill when a feature is too large for a single agent pass and must be split into independently verifiable work units.
当一个功能太大，无法在单次 Agent 执行中完成，必须拆分为独立可验证的工作单元时使用此技能。

## Pipeline Stages
## 流水线阶段

1. RFC intake
1. RFC 受理
2. DAG decomposition
2. DAG 分解
3. Unit assignment
3. 单元分配
4. Unit implementation
4. 单元实现
5. Unit validation
5. 单元验证
6. Merge queue and integration
6. 合并队列与集成
7. Final system verification
7. 最终系统验证

## Unit Spec Template
## 单元规格模板

Each work unit should include:
每个工作单元应包含：

- `id`
- `depends_on`
- `scope`
- `acceptance_tests`
- `risk_level`
- `rollback_plan`

## Complexity Tiers
## 复杂度分层

- Tier 1: isolated file edits, deterministic tests
- Tier 1: 隔离的文件编辑、确定性测试
- Tier 2: multi-file behavior changes, moderate integration risk
- Tier 2: 多文件行为变更、中等集成风险
- Tier 3: schema/auth/perf/security changes
- Tier 3: schema/认证/性能/安全变更

## Quality Pipeline per Unit
## 每个单元的质量流水线

1. research
1. 研究
2. implementation plan
2. 实现计划
3. implementation
3. 实现
4. tests
4. 测试
5. review
5. 审查
6. merge-ready report
6. 可合并报告

## Merge Queue Rules
## 合并队列规则

- Never merge a unit with unresolved dependency failures.
- 永远不要合并存在未解决依赖失败的单元。
- Always rebase unit branches on latest integration branch.
- 始终将单元分支 rebase 到最新的集成分支。
- Re-run integration tests after each queued merge.
- 每次排队的合并后重新运行集成测试。

## Recovery
## 恢复

If a unit stalls:
如果某个单元停滞：

- evict from active queue
- 从活跃队列中驱逐
- snapshot findings
- 快照记录发现
- regenerate narrowed unit scope
- 重新生成缩小的单元范围
- retry with updated constraints
- 使用更新的约束重试

## Outputs
## 输出

- RFC execution log
- RFC 执行日志
- unit scorecards
- 单元记分卡
- dependency graph snapshot
- 依赖图快照
- integration risk summary
- 集成风险摘要
