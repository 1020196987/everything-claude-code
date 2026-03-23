# CLAUDE.md
# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.
此文件为 Claude Code (claude.ai/code) 在此仓库中工作时提供指导。

## Project Overview
## 项目概述

This is a **Claude Code plugin** - a collection of production-ready agents, skills, hooks, commands, rules, and MCP configurations. The project provides battle-tested workflows for software development using Claude Code.
这是一个 **Claude Code 插件** —— 包含生产就绪的 agents、skills、hooks、commands、rules 和 MCP 配置。该项目为使用 Claude Code 的软件开发提供了经过实战检验的工作流。

## Running Tests
## 运行测试

```bash
# Run all tests
node tests/run-all.js
# 运行所有测试
node tests/run-all.js

# Run individual test files
node tests/lib/utils.test.js
node tests/lib/package-manager.test.js
node tests/hooks/hooks.test.js
# 运行单独的测试文件
node tests/lib/utils.test.js
node tests/lib/package-manager.test.js
node tests/hooks/hooks.test.js
```

## Architecture
## 架构

The project is organized into several core components:
项目由以下几个核心组件组成：

- **agents/** - Specialized subagents for delegation (planner, code-reviewer, tdd-guide, etc.)
  **agents/** - 用于委托的专业化子代理 (planner, code-reviewer, tdd-guide 等)
- **skills/** - Workflow definitions and domain knowledge (coding standards, patterns, testing)
  **skills/** - 工作流定义和领域知识 (编码标准、模式、测试)
- **commands/** - Slash commands invoked by users (/tdd, /plan, /e2e, etc.)
  **commands/** - 用户调用的斜杠命令 (/tdd, /plan, /e2e 等)
- **hooks/** - Trigger-based automations (session persistence, pre/post-tool hooks)
  **hooks/** - 基于触发器的自动化 (会话持久化、工具前后钩子)
- **rules/** - Always-follow guidelines (security, coding style, testing requirements)
  **rules/** - 始终遵循的指南 (安全、编码风格、测试要求)
- **mcp-configs/** - MCP server configurations for external integrations
  **mcp-configs/** - MCP 服务器配置，用于外部集成
- **scripts/** - Cross-platform Node.js utilities for hooks and setup
  **scripts/** - 用于钩子和设置的跨平台 Node.js 工具
- **tests/** - Test suite for scripts and utilities
  **tests/** - 脚本和工具的测试套件

## Key Commands
## 关键命令

- `/tdd` - Test-driven development workflow
  `/tdd` - 测试驱动开发工作流
- `/plan` - Implementation planning
  `/plan` - 实现规划
- `/e2e` - Generate and run E2E tests
  `/e2e` - 生成并运行 E2E 测试
- `/code-review` - Quality review
  `/code-review` - 质量审查
- `/build-fix` - Fix build errors
  `/build-fix` - 修复构建错误
- `/learn` - Extract patterns from sessions
  `/learn` - 从会话中提取模式
- `/skill-create` - Generate skills from git history
  `/skill-create` - 从 git 历史生成 skills

## Development Notes
## 开发说明

- Package manager detection: npm, pnpm, yarn, bun (configurable via `CLAUDE_PACKAGE_MANAGER` env var or project config)
  包管理器检测: npm, pnpm, yarn, bun (可通过 `CLAUDE_PACKAGE_MANAGER` 环境变量或项目配置)
- Cross-platform: Windows, macOS, Linux support via Node.js scripts
  **跨平台**: 通过 Node.js 脚本支持 Windows、macOS、Linux
- Agent format: Markdown with YAML frontmatter (name, description, tools, model)
  **Agent 格式**: Markdown 配合 YAML frontmatter (name, description, tools, model)
- Skill format: Markdown with clear sections for when to use, how it works, examples
  **Skill 格式**: Markdown 带有清晰的使用时机、工作原理、示例部分
- Hook format: JSON with matcher conditions and command/notification hooks
  **Hook 格式**: JSON 配合匹配条件和命令/通知钩子

## Contributing
## 贡献指南

Follow the formats in CONTRIBUTING.md:
遵循 CONTRIBUTING.md 中的格式：

- Agents: Markdown with frontmatter (name, description, tools, model)
  Agents: Markdown 配合 frontmatter (name, description, tools, model)
- Skills: Clear sections (When to Use, How It Works, Examples)
  Skills: 清晰部分 (使用时机、工作原理、示例)
- Commands: Markdown with description frontmatter
  Commands: Markdown 配合描述 frontmatter
- Hooks: JSON with matcher and hooks array
  Hooks: JSON 配合 matcher 和 hooks 数组

File naming: lowercase with hyphens (e.g., `python-reviewer.md`, `tdd-workflow.md`)
**文件命名**: 小写字母加连字符 (例如 `python-reviewer.md`, `tdd-workflow.md`)
