---
name: promote
description: Promote project-scoped instincts to global scope
command: true
description-zh: 将项目范围的本能升级到全局范围
---

# Promote Command
# Promote 命令

Promote instincts from project scope to global scope in continuous-learning-v2.
在 continuous-learning-v2 中将本能从项目范围升级到全局范围。

## Usage
## 使用方式

```bash
/promote                      # Auto-detect promotion candidates
/promote --dry-run            # Preview auto-promotion candidates
/promote --force              # Promote all qualified candidates without prompt
/promote grep-before-edit     # Promote one specific instinct from current project
```

```bash
/promote                      # 自动检测升级候选
/promote --dry-run            # 预览自动升级候选
/promote --force              # 不提示直接升级所有符合条件的候选
/promote grep-before-edit     # 从当前项目升级一个特定的本能
```

## What to Do
## 做什么

1. Detect current project
   检测当前项目
2. If `instinct-id` is provided, promote only that instinct (if present in current project)
   如果提供了 `instinct-id`，则仅升级该本能（如果存在于当前项目中）
3. Otherwise, find cross-project candidates that:
   否则，查找跨项目候选：
   - Appear in at least 2 projects
     出现在至少 2 个项目中
   - Meet confidence threshold
     满足置信度阈值
4. Write promoted instincts to `~/.claude/homunculus/instincts/personal/` with `scope: global`
   将升级的本能写入 `~/.claude/homunculus/instincts/personal/`，并设置 `scope: global`
