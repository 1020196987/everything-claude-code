# Model Route Command
# 模型路由命令

Recommend the best model tier for the current task by complexity and budget.
根据复杂度和预算为当前任务推荐最佳模型层。

## Usage
## 使用方式

`/model-route [task-description] [--budget low|med|high]`

## Routing Heuristic
## 路由启发式

- `haiku`: deterministic, low-risk mechanical changes
- `haiku`：确定性、低风险机械更改

- `sonnet`: default for implementation and refactors
- `sonnet`：实现和重构的默认选项

- `opus`: architecture, deep review, ambiguous requirements
- `opus`：架构、深度审查、模糊需求

## Required Output
## 必需输出

- recommended model
- 推荐的模型

- confidence level
- 置信度级别

- why this model fits
- 为什么此模型适合

- fallback model if first attempt fails
- 如果第一次尝试失败的后备模型

## Arguments
## 参数

$ARGUMENTS:
- `[task-description]` optional free-text
- `[task-description]` 可选自由文本
- `--budget low|med|high` optional
- `--budget low|med|high` 可选
