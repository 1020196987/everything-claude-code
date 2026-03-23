---
name: instinct-status
description: Show learned instincts (project + global) with confidence
description-zh: 显示已学习的本能（项目 + 全局）及置信度
command: true
---

# Instinct Status Command
# Instinct 状态命令

Shows learned instincts for the current project plus global instincts, grouped by domain.
显示当前项目的已学习本能以及全局本能，按域分组。

## Implementation
## 实现

Run the instinct CLI using the plugin root path:
使用插件根路径运行 instinct CLI：

```bash
python3 "${CLAUDE_PLUGIN_ROOT}/skills/continuous-learning-v2/scripts/instinct-cli.py" status
```

Or if `CLAUDE_PLUGIN_ROOT` is not set (manual installation), use:
或者如果 `CLAUDE_PLUGIN_ROOT` 未设置（手动安装），使用：

```bash
python3 ~/.claude/skills/continuous-learning-v2/scripts/instinct-cli.py status
```

## Usage
## 使用方式

```
/instinct-status
```

## What to Do
## 做什么

1. Detect current project context (git remote/path hash)
1. 检测当前项目上下文（git remote/path hash）

2. Read project instincts from `~/.claude/homunculus/projects/<project-id>/instincts/`
2. 从 `~/.claude/homunculus/projects/<project-id>/instincts/` 读取项目本能

3. Read global instincts from `~/.claude/homunculus/instincts/`
3. 从 `~/.claude/homunculus/instincts/` 读取全局本能

4. Merge with precedence rules (project overrides global when IDs collide)
4. 按优先级规则合并（当 ID 冲突时项目覆盖全局）

5. Display grouped by domain with confidence bars and observation stats
5. 按域分组显示，带置信度条和观察统计

## Output Format
## 输出格式

```
============================================================
  INSTINCT STATUS - 12 total
============================================================

  Project: my-app (a1b2c3d4e5f6)
  Project instincts: 8
  Global instincts:  4

## PROJECT-SCOPED (my-app)
  ### WORKFLOW (3)
    ███████░░░  70%  grep-before-edit [project]
              trigger: when modifying code

## GLOBAL (apply to all projects)
  ### SECURITY (2)
    █████████░  85%  validate-user-input [global]
              trigger: when handling user input
```
