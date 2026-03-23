---
name: instinct-import
description: Import instincts from file or URL into project/global scope
description-zh: 将本能从文件或 URL 导入到项目/全局范围
command: true
---

# Instinct Import Command
# Instinct 导入命令

Import instincts from local file paths or HTTP(S) URLs.
从本地文件路径或 HTTP(S) URL 导入本能。

## Usage
## 使用方式

```
/instinct-import team-instincts.yaml
/instinct-import team-instincts.yaml
/instinct-import https://github.com/org/repo/instincts.yaml
/instinct-import https://github.com/org/repo/instincts.yaml
/instinct-import team-instincts.yaml --dry-run
/instinct-import team-instincts.yaml --dry-run
/instinct-import team-instincts.yaml --scope global --force
/instinct-import team-instincts.yaml --scope global --force
```

## What to Do
## 做什么

1. Fetch the instinct file (local path or URL)
1. 获取本能文件（本地路径或 URL）

2. Parse and validate the format
2. 解析并验证格式

3. Check for duplicates with existing instincts
3. 检查与现有本能的重复

4. Merge or add new instincts
4. 合并或添加新本能

5. Save to inherited instincts directory:
5. 保存到继承的本能目录：

   - Project scope: `~/.claude/homunculus/projects/<project-id>/instincts/inherited/`
   - 项目范围：`~/.claude/homunculus/projects/<project-id>/instincts/inherited/`

   - Global scope: `~/.claude/homunculus/instincts/inherited/`
   - 全局范围：`~/.claude/homunculus/instincts/inherited/`

## Import Process
## 导入过程

```
📥 Importing instincts from: team-instincts.yaml
================================================

Found 12 instincts to import.

Analyzing conflicts...

## New Instincts (8)
These will be added:
  ✓ use-zod-validation (confidence: 0.7)
  ✓ prefer-named-exports (confidence: 0.65)
  ✓ test-async-functions (confidence: 0.8)
  ...

## Duplicate Instincts (3)
Already have similar instincts:
  ⚠️ prefer-functional-style
     Local: 0.8 confidence, 12 observations
     Import: 0.7 confidence
     → Keep local (higher confidence)

  ⚠️ test-first-workflow
     Local: 0.75 confidence
     Import: 0.9 confidence
     → Update to import (higher confidence)

Import 8 new, update 1?
```

## Merge Behavior
## 合并行为

When importing an instinct with an existing ID:
当导入具有现有 ID 的本能时：

- Higher-confidence import becomes an update candidate
- 更高置信度的导入成为更新候选

- Equal/lower-confidence import is skipped
- 相同/更低置信度的导入被跳过

- User confirms unless `--force` is used
- 除非使用 `--force`，否则需要用户确认

## Source Tracking
## 来源追踪

Imported instincts are marked with:
导入的本能会标记为：

```yaml
source: inherited
scope: project
imported_from: "team-instincts.yaml"
project_id: "a1b2c3d4e5f6"
project_name: "my-project"
```

## Flags
## 标志

- `--dry-run`: Preview without importing
- `--dry-run`：预览而不导入

- `--force`: Skip confirmation prompt
- `--force`：跳过确认提示

- `--min-confidence <n>`: Only import instincts above threshold
- `--min-confidence <n>`：仅导入高于阈值的本能

- `--scope <project|global>`: Select target scope (default: `project`)
- `--scope <project|global>`：选择目标范围（默认：`project`）

## Output
## 输出

After import:
导入后：

```
✅ Import complete!

Added: 8 instincts
Updated: 1 instinct
Skipped: 3 instincts (equal/higher confidence already exists)

New instincts saved to: ~/.claude/homunculus/instincts/inherited/

Run /instinct-status to see all instincts.
```
