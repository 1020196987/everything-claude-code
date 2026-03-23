---
name: Hooks
description: Hooks system and best practices
description: Hooks 系统和最佳实践
---

# Hooks System
# Hooks 系统

## Hook Types
## Hook 类型

- **PreToolUse**: Before tool execution (validation, parameter modification)
  **PreToolUse**：工具执行前（验证、参数修改）
- **PostToolUse**: After tool execution (auto-format, checks)
  **PostToolUse**：工具执行后（自动格式化、检查）
- **Stop**: When session ends (final verification)
  **Stop**：会话结束时（最终验证）

## Auto-Accept Permissions
## 自动接受权限

Use with caution:
谨慎使用：

- Enable for trusted, well-defined plans
  为可信的、定义明确的计划启用
- Disable for exploratory work
  为探索性工作禁用
- Never use dangerously-skip-permissions flag
  永不使用 dangerously-skip-permissions 标志
- Configure `allowedTools` in `~/.claude.json` instead
  改为在 `~/.claude.json` 中配置 `allowedTools`

## TodoWrite Best Practices
## TodoWrite 最佳实践

Use TodoWrite tool to:
使用 TodoWrite 工具来：

- Track progress on multi-step tasks
  跟踪多步骤任务的进度
- Verify understanding of instructions
  验证对指令的理解
- Enable real-time steering
  实现实时引导
- Show granular implementation steps
  显示细粒度的实现步骤

Todo list reveals:
Todo 列表揭示：

- Out of order steps
  乱序的步骤
- Missing items
  缺失的项目
- Extra unnecessary items
  多余的不必要项目
- Wrong granularity
  错误的粒度
- Misinterpreted requirements
  误解的需求
