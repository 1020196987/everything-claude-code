---
name: Performance
description: Performance optimization and model selection strategy
description: 性能优化和模型选择策略
---

# Performance Optimization
# 性能优化

## Model Selection Strategy
## 模型选择策略

**Haiku 4.5** (90% of Sonnet capability, 3x cost savings):
**Haiku 4.5**（Sonnet 90% 能力，节省 3 倍成本）：

- Lightweight agents with frequent invocation
  频繁调用的轻量级 agent
- Pair programming and code generation
  结对编程和代码生成
- Worker agents in multi-agent systems
  多 agent 系统中的工作 agent

**Sonnet 4.6** (Best coding model):
**Sonnet 4.6**（最佳编码模型）：

- Main development work
  主要开发工作
- Orchestrating multi-agent workflows
  编排多 agent 工作流
- Complex coding tasks
  复杂编码任务

**Opus 4.5** (Deepest reasoning):
**Opus 4.5**（最深推理）：

- Complex architectural decisions
  复杂架构决策
- Maximum reasoning requirements
  最大推理需求
- Research and analysis tasks
  研究和分析任务

## Context Window Management
## 上下文窗口管理

Avoid last 20% of context window for:
避免使用上下文窗口的最后 20%：

- Large-scale refactoring
  大规模重构
- Feature implementation spanning multiple files
  跨多个文件的功能实现
- Debugging complex interactions
  调试复杂交互

Lower context sensitivity tasks:
低上下文敏感度任务：

- Single-file edits
  单文件编辑
- Independent utility creation
  独立工具创建
- Documentation updates
  文档更新
- Simple bug fixes
  简单 bug 修复

## Extended Thinking + Plan Mode
## 扩展思考 + 计划模式

Extended thinking is enabled by default, reserving up to 31,999 tokens for internal reasoning.
扩展思考默认启用，为内部推理保留最多 31,999 个 token。

Control extended thinking via:
通过以下方式控制扩展思考：

- **Toggle**: Option+T (macOS) / Alt+T (Windows/Linux)
  **切换**：Option+T (macOS) / Alt+T (Windows/Linux)
- **Config**: Set `alwaysThinkingEnabled` in `~/.claude/settings.json`
  **配置**：在 `~/.claude/settings.json` 中设置 `alwaysThinkingEnabled`
- **Budget cap**: `export MAX_THINKING_TOKENS=10000`
  **预算上限**：`export MAX_THINKING_TOKENS=10000`
- **Verbose mode**: Ctrl+O to see thinking output
  **详细模式**：Ctrl+O 查看思考输出

For complex tasks requiring deep reasoning:
对于需要深度推理的复杂任务：

1. Ensure extended thinking is enabled (on by default)
   确保扩展思考已启用（默认开启）
2. Enable **Plan Mode** for structured approach
   启用**计划模式**以获得结构化方法
3. Use multiple critique rounds for thorough analysis
   使用多轮评审进行彻底分析
4. Use split role sub-agents for diverse perspectives
   使用分裂角色的子 agent 获得多元视角

## Build Troubleshooting
## 构建故障排除

If build fails:
如果构建失败：

1. Use **build-error-resolver** agent
   使用 **build-error-resolver** agent
2. Analyze error messages
   分析错误消息
3. Fix incrementally
   逐步修复
4. Verify after each fix
   每次修复后验证
