# Loop Start Command
# Loop 启动命令

Start a managed autonomous loop pattern with safety defaults.
启动具有安全默认值的托管自主循环模式。

## Usage
## 使用方式

`/loop-start [pattern] [--mode safe|fast]`

- `pattern`: `sequential`, `continuous-pr`, `rfc-dag`, `infinite`
- `pattern`：`sequential`、`continuous-pr`、`rfc-dag`、`infinite`

- `--mode`:
- `--mode`：

  - `safe` (default): strict quality gates and checkpoints
  - `safe`（默认）：严格的质量门和检查点

  - `fast`: reduced gates for speed
  - `fast`：减少的门控以提高速度

## Flow
## 流程

1. Confirm repository state and branch strategy.
1. 确认仓库状态和分支策略。

2. Select loop pattern and model tier strategy.
2. 选择循环模式和模型层级策略。

3. Enable required hooks/profile for the chosen mode.
3. 为所选模式启用所需的 hooks/profile。

4. Create loop plan and write runbook under `.claude/plans/`.
4. 创建循环计划并在 `.claude/plans/` 下编写操作手册。

5. Print commands to start and monitor the loop.
5. 打印启动和监控循环的命令。

## Required Safety Checks
## 所需安全检查

- Verify tests pass before first loop iteration.
- 在第一次循环迭代前验证测试通过。

- Ensure `ECC_HOOK_PROFILE` is not disabled globally.
- 确保 `ECC_HOOK_PROFILE` 未全局禁用。

- Ensure loop has explicit stop condition.
- 确保循环有明确的停止条件。

## Arguments
## 参数

$ARGUMENTS:
- `<pattern>` optional (`sequential|continuous-pr|rfc-dag|infinite`)
- `<pattern>` 可选（`sequential|continuous-pr|rfc-dag|infinite`）
- `--mode safe|fast` optional
- `--mode safe|fast` 可选
