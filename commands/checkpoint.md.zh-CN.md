---
description: Create or verify a named checkpoint in your workflow to track progress and compare state.
description-zh: 在工作流程中创建或验证命名检查点，以跟踪进度和比较状态。
---

# Checkpoint Command
# 检查点命令

Create or verify a checkpoint in your workflow.
在您的工作流程中创建或验证检查点。

## Usage
## 使用方式

`/checkpoint [create|verify|list] [name]`

## Create Checkpoint
## 创建检查点

When creating a checkpoint:
创建检查点时：

1. Run `/verify quick` to ensure current state is clean
   运行 `/verify quick` 确保当前状态干净
2. Create a git stash or commit with checkpoint name
   创建带有检查点名称的 git stash 或提交
3. Log checkpoint to `.claude/checkpoints.log`:
   将检查点记录到 `.claude/checkpoints.log`：

```bash
echo "$(date +%Y-%m-%d-%H:%M) | $CHECKPOINT_NAME | $(git rev-parse --short HEAD)" >> .claude/checkpoints.log
```

4. Report checkpoint created
   报告检查点已创建

## Verify Checkpoint
## 验证检查点

When verifying against a checkpoint:
验证检查点时：

1. Read checkpoint from log
   从日志读取检查点
2. Compare current state to checkpoint:
   将当前状态与检查点比较：
   - Files added since checkpoint
     检查点后添加的文件
   - Files modified since checkpoint
     检查点后修改的文件
   - Test pass rate now vs then
     现在的测试通过率 vs 当时
   - Coverage now vs then
     现在的覆盖率 vs 当时

3. Report:
   报告：

```
CHECKPOINT COMPARISON: $NAME
============================
Files changed: X
Tests: +Y passed / -Z failed
Coverage: +X% / -Y%
Build: [PASS/FAIL]
```

## List Checkpoints
## 列出检查点

Show all checkpoints with:
显示所有检查点：
- Name
  名称
- Timestamp
  时间戳
- Git SHA
  Git SHA
- Status (current, behind, ahead)
  状态（current、behind、ahead）

## Workflow
## 工作流程

Typical checkpoint flow:
典型的检查点流程：

```
[Start] --> /checkpoint create "feature-start"
   |
[Implement] --> /checkpoint create "core-done"
   |
[Test] --> /checkpoint verify "core-done"
   |
[Refactor] --> /checkpoint create "refactor-done"
   |
[PR] --> /checkpoint verify "feature-start"
```

## Arguments
## 参数

$ARGUMENTS:
- `create <name>` - Create named checkpoint
  创建命名检查点
- `verify <name>` - Verify against named checkpoint
  针对命名检查点验证
- `list` - Show all checkpoints
  显示所有检查点
- `clear` - Remove old checkpoints (keeps last 5)
  删除旧的检查点（保留最后 5 个）
