# Loop Status Command
# Loop 状态命令

Inspect active loop state, progress, and failure signals.
检查活动循环状态、进度和失败信号。

## Usage
## 使用方式

`/loop-status [--watch]`

## What to Report
## 报告内容

- active loop pattern
- 活动循环模式

- current phase and last successful checkpoint
- 当前阶段和最后成功的检查点

- failing checks (if any)
- 失败的检查（如有）

- estimated time/cost drift
- 估计的时间/成本偏差

- recommended intervention (continue/pause/stop)
- 建议的干预（继续/暂停/停止）

## Watch Mode
## 监视模式

When `--watch` is present, refresh status periodically and surface state changes.
当存在 `--watch` 时，定期刷新状态并显示状态变化。

## Arguments
## 参数

$ARGUMENTS:
- `--watch` optional
- `--watch` 可选
