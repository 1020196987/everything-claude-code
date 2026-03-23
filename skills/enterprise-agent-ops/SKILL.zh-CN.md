---
name: enterprise-agent-ops
description: Operate long-lived agent workloads with observability, security boundaries, and lifecycle management.
description zh-CN: 通过可观测性、安全边界和生命周期管理来运营长期运行的代理工作负载。
origin: ECC
---

# Enterprise Agent Ops

## Enterprise Agent Ops
## 企业级代理运营

Use this skill for cloud-hosted or continuously running agent systems that need operational controls beyond single CLI sessions.
将此技能用于需要超越单一CLI会话的运营控制的云托管或持续运行的代理系统。

## Operational Domains

## Operational Domains
## 运营领域

1. runtime lifecycle (start, pause, stop, restart)
   运行时生命周期（启动、暂停、停止、重启）
2. observability (logs, metrics, traces)
   可观测性（日志、指标、追踪）
3. safety controls (scopes, permissions, kill switches)
   安全控制（范围、权限、终止开关）
4. change management (rollout, rollback, audit)
   变更管理（发布、回滚、审计）

## Baseline Controls

## Baseline Controls
## 基线控制

- immutable deployment artifacts
  不可变的部署制品
- least-privilege credentials
  最小权限凭证
- environment-level secret injection
  环境级密钥注入
- hard timeout and retry budgets
  硬超时和重试预算
- audit log for high-risk actions
  高风险操作审计日志

## Metrics to Track

## Metrics to Track
## 需追踪的指标

- success rate
  成功率
- mean retries per task
  每个任务的平均重试次数
- time to recovery
  恢复时间
- cost per successful task
  每个成功任务的成本
- failure class distribution
  故障类别分布

## Incident Pattern

## Incident Pattern
## 事件模式

When failure spikes:
当故障激增时：

1. freeze new rollout
   冻结新发布
2. capture representative traces
   捕获代表性追踪
3. isolate failing route
   隔离故障路由
4. patch with smallest safe change
   用最小安全变更修补
5. run regression + security checks
   运行回归测试和安全检查
6. resume gradually
   逐步恢复

## Deployment Integrations

## Deployment Integrations
## 部署集成

This skill pairs with:
此技能与以下工具配合：

- PM2 workflows
  PM2工作流
- systemd services
  systemd服务
- container orchestrators
  容器编排器
- CI/CD gates
  CI/CD关卡
