# Changelog
# 变更日志

## 1.8.0 - 2026-03-04

### Highlights
### 亮点

- Harness-first release focused on reliability, eval discipline, and autonomous loop operations.
- 首个以 harness 为重点的发布，专注于可靠性、eval 规范和自主循环操作。
- Hook runtime now supports profile-based control and targeted hook disabling.
- Hook 运行时现在支持基于 profile 的控制和定向 hook 禁用。
- NanoClaw v2 adds model routing, skill hot-load, branching, search, compaction, export, and metrics.
- NanoClaw v2 新增模型路由、skill 热加载、分支、搜索、压缩、导出和指标功能。

### Core
### 核心

- Added new commands: `/harness-audit`, `/loop-start`, `/loop-status`, `/quality-gate`, `/model-route`.
- 新增命令: `/harness-audit`, `/loop-start`, `/loop-status`, `/quality-gate`, `/model-route`。
- Added new skills:
- 新增 skills:
  - `agent-harness-construction`
  - `agentic-engineering`
  - `ralphinho-rfc-pipeline`
  - `ai-first-engineering`
  - `enterprise-agent-ops`
  - `nanoclaw-repl`
  - `continuous-agent-loop`
- Added new agents:
- 新增 agents:
  - `harness-optimizer`
  - `loop-operator`

### Hook Reliability
### Hook 可靠性

- Fixed SessionStart root resolution with robust fallback search.
- 修复了 SessionStart 根目录解析问题，添加了健壮的回退搜索。
- Moved session summary persistence to `Stop` where transcript payload is available.
- 将会话摘要持久化移至 `Stop` 阶段，此时才有转录 payload 可用。
- Added quality-gate and cost-tracker hooks.
- 新增 quality-gate 和 cost-tracker hooks。
- Replaced fragile inline hook one-liners with dedicated script files.
- 用专用脚本文件替换了脆弱的内联 hook 一行代码。
- Added `ECC_HOOK_PROFILE` and `ECC_DISABLED_HOOKS` controls.
- 新增 `ECC_HOOK_PROFILE` 和 `ECC_DISABLED_HOOKS` 控制选项。

### Cross-Platform
### 跨平台

- Improved Windows-safe path handling in doc warning logic.
- 改进了文档警告逻辑中的 Windows 安全路径处理。
- Hardened observer loop behavior to avoid non-interactive hangs.
- 加强了观察者循环行为以避免非交互式挂起。

### Notes
### 说明

- `autonomous-loops` is kept as a compatibility alias for one release; `continuous-agent-loop` is the canonical name.
- `autonomous-loops` 保留为兼容性别名一个发布周期；`continuous-agent-loop` 是正式名称。

### Credits
### 致谢

- inspired by [zarazhangrui](https://github.com/zarazhangrui)
- 灵感来源: [zarazhangrui](https://github.com/zarazhangrui)
- homunculus-inspired by [humanplane](https://github.com/humanplane)
- homunculus 灵感: [humanplane](https://github.com/humanplane)
