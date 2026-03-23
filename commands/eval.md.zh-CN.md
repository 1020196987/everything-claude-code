---
description: Manage eval-driven development workflow. Define, check, and report on capability and regression evals.
description-zh: 管理评估驱动开发工作流。定义、检查和报告能力和回归评估。
---

# Eval Command
# Eval 命令

Manage eval-driven development workflow.
管理评估驱动开发工作流。

## Usage
## 使用方式

`/eval [define|check|report|list] [feature-name]`

## Define Evals
## 定义评估

`/eval define feature-name`

Create a new eval definition:
创建新的评估定义：

1. Create `.claude/evals/feature-name.md` with template:
   使用模板创建 `.claude/evals/feature-name.md`：

```markdown
## EVAL: feature-name
Created: $(date)

### Capability Evals
- [ ] [Description of capability 1]
- [ ] [Description of capability 2]

### Regression Evals
- [ ] [Existing behavior 1 still works]
- [ ] [Existing behavior 2 still works]

### Success Criteria
- pass@3 > 90% for capability evals
- pass^3 = 100% for regression evals
```

2. Prompt user to fill in specific criteria
   提示用户填写具体标准

## Check Evals
## 检查评估

`/eval check feature-name`

Run evals for a feature:
为功能运行评估：

1. Read eval definition from `.claude/evals/feature-name.md`
   从 `.claude/evals/feature-name.md` 读取评估定义
2. For each capability eval:
   对于每个能力评估：
   - Attempt to verify criterion
     尝试验证标准
   - Record PASS/FAIL
     记录 PASS/FAIL
   - Log attempt in `.claude/evals/feature-name.log`
     在 `.claude/evals/feature-name.log` 记录尝试
3. For each regression eval:
   对于每个回归评估：
   - Run relevant tests
     运行相关测试
   - Compare against baseline
     与基线比较
   - Record PASS/FAIL
     记录 PASS/FAIL

## Report Evals
## 报告评估

`/eval report feature-name`

Generate comprehensive eval report:
生成综合评估报告：

```
EVAL REPORT: feature-name
=========================
Generated: $(date)

CAPABILITY EVALS
----------------
[eval-1]: PASS (pass@1)
[eval-2]: PASS (pass@2) - required retry
[eval-3]: FAIL - see notes

REGRESSION EVALS
----------------
[test-1]: PASS
[test-2]: PASS
[test-3]: PASS

METRICS
-------
Capability pass@1: 67%
Capability pass@3: 100%
Regression pass^3: 100%

NOTES
-----
[Any issues, edge cases, or observations]

RECOMMENDATION
--------------
[SHIP / NEEDS WORK / BLOCKED]
```

## Arguments
## 参数

$ARGUMENTS:
- `define <name>` - Create new eval definition
  创建新的评估定义
- `check <name>` - Run and check evals
  运行和检查评估
- `report <name>` - Generate full report
  生成完整报告
- `list` - Show all evals
  显示所有评估
- `clean` - Remove old eval logs (keeps last 10 runs)
  删除旧的评估日志（保留最近 10 次运行）
