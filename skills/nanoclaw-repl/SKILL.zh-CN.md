---
name: nanoclaw-repl
description: Operate and extend NanoClaw v2, ECC's zero-dependency session-aware REPL built on claude -p.
description zh-CN: 操作和扩展 NanoClaw v2，ECC 基于 claude -p 构建的零依赖、会话感知的 REPL。
origin: ECC
---

# NanoClaw REPL

## NanoClaw REPL
## NanoClaw REPL

Use this skill when running or extending `scripts/claw.js`.
在运行或扩展 `scripts/claw.js` 时使用此技能。

## Capabilities

## Capabilities
## 功能

- persistent markdown-backed sessions
  - 持久化的 markdown 支持的会话
- model switching with `/model`
  - 使用 `/model` 切换模型
- dynamic skill loading with `/load`
  - 使用 `/load` 动态加载技能
- session branching with `/branch`
  - 使用 `/branch` 进行会话分支
- cross-session search with `/search`
  - 使用 `/search` 跨会话搜索
- history compaction with `/compact`
  - 使用 `/compact` 进行历史压缩
- export to md/json/txt with `/export`
  - 使用 `/export` 导出为 md/json/txt
- session metrics with `/metrics`
  - 使用 `/metrics` 查看会话指标

## Operating Guidance

## Operating Guidance
## 操作指南

1. Keep sessions task-focused.
   - 保持会话任务专注。
2. Branch before high-risk changes.
   - 在高风险更改之前进行分支。
3. Compact after major milestones.
   - 在主要里程碑后进行压缩。
4. Export before sharing or archival.
   - 在共享或归档之前导出。

## Extension Rules

## Extension Rules
## 扩展规则

- keep zero external runtime dependencies
  - 保持零外部运行时依赖
- preserve markdown-as-database compatibility
  - 保持 markdown 即数据库的兼容性
- keep command handlers deterministic and local
  - 保持命令处理程序确定性和本地化
