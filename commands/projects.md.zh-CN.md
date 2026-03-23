---
name: projects
description: List known projects and their instinct statistics
description-zh: 列出已知项目及其本能统计
command: true
---

# Projects Command
# Projects 命令

List project registry entries and per-project instinct/observation counts for continuous-learning-v2.
列出项目注册条目和每个项目的本能/观察计数，用于 continuous-learning-v2。

## Implementation
## 实现

Run the instinct CLI using the plugin root path:
使用插件根路径运行 instinct CLI：

```bash
python3 "${CLAUDE_PLUGIN_ROOT}/skills/continuous-learning-v2/scripts/instinct-cli.py" projects
```

Or if `CLAUDE_PLUGIN_ROOT` is not set (manual installation), use:
或者如果 `CLAUDE_PLUGIN_ROOT` 未设置（手动安装），使用：

```bash
python3 ~/.claude/skills/continuous-learning-v2/scripts/instinct-cli.py projects
```

## Usage
## 使用方式

```bash
/projects
```

## What to Do
## 做什么

1. Read `~/.claude/homunculus/projects.json`
1. 读取 `~/.claude/homunculus/projects.json`

2. For each project, display:
2. 对于每个项目，显示：

   - Project name, id, root, remote
   - 项目名称、id、root、remote

   - Personal and inherited instinct counts
   - 个人和继承的本能计数

   - Observation event count
   - 观察事件计数

   - Last seen timestamp
   - 最后一次看到的时间戳

3. Also display global instinct totals
3. 也显示全局本能总数
