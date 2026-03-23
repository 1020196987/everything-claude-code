---
name: eval-harness
description: Formal evaluation framework for Claude Code sessions implementing eval-driven development (EDD) principles
description zh-CN: Claude Code会话的正式评估框架，实现评估驱动开发（EDD）原则
origin: ECC
tools: Read, Write, Edit, Bash, Grep, Glob
---

# Eval Harness Skill

## Eval Harness Skill
## 评估工具技能

A formal evaluation framework for Claude Code sessions, implementing eval-driven development (EDD) principles.
Claude Code会话的正式评估框架，实现评估驱动开发（EDD）原则。

## When to Activate

## When to Activate
## 何时激活

- Setting up eval-driven development (EDD) for AI-assisted workflows
  为AI辅助工作流设置评估驱动开发（EDD）
- Defining pass/fail criteria for Claude Code task completion
  定义Claude Code任务完成的通过/失败标准
- Measuring agent reliability with pass@k metrics
  使用pass@k指标衡量代理可靠性
- Creating regression test suites for prompt or agent changes
  为提示或代理变更创建回归测试套件
- Benchmarking agent performance across model versions
  在模型版本间对代理性能进行基准测试

## Philosophy

## Philosophy
## 理念

Eval-Driven Development treats evals as the "unit tests of AI development":
评估驱动开发将评估视为"AI开发的单元测试"：

- Define expected behavior BEFORE implementation
  在实现之前定义预期行为
- Run evals continuously during development
  在开发过程中持续运行评估
- Track regressions with each change
  每次变更追踪回归
- Use pass@k metrics for reliability measurement
  使用pass@k指标进行可靠性测量

## Eval Types

## Eval Types
## 评估类型

### Capability Evals

### Capability Evals
### 能力评估

Test if Claude can do something it couldn't before:
测试Claude是否能做以前做不到的事情：

```markdown
[CAPABILITY EVAL: feature-name]
Task: Description of what Claude should accomplish
Success Criteria:
  - [ ] Criterion 1
  - [ ] Criterion 2
  - [ ] Criterion 3
Expected Output: Description of expected result
```

### Regression Evals

### Regression Evals
### 回归评估

Ensure changes don't break existing functionality:
确保变更不会破坏现有功能：

```markdown
[REGRESSION EVAL: feature-name]
Baseline: SHA or checkpoint name
Tests:
  - existing-test-1: PASS/FAIL
  - existing-test-2: PASS/FAIL
  - existing-test-3: PASS/FAIL
Result: X/Y passed (previously Y/Y)
```

## Grader Types

## Grader Types
## 评分器类型

### 1. Code-Based Grader

### 1. Code-Based Grader
### 1. 基于代码的评分器

Deterministic checks using code:
使用代码进行确定性检查：

```bash
# Check if file contains expected pattern
grep -q "export function handleAuth" src/auth.ts && echo "PASS" || echo "FAIL"

# Check if tests pass
npm test -- --testPathPattern="auth" && echo "PASS" || echo "FAIL"

# Check if build succeeds
npm run build && echo "PASS" || echo "FAIL"
```

### 2. Model-Based Grader

### 2. Model-Based Grader
### 2. 基于模型的评分器

Use Claude to evaluate open-ended outputs:
使用Claude评估开放式输出：

```markdown
[MODEL GRADER PROMPT]
Evaluate the following code change:
1. Does it solve the stated problem?
2. Is it well-structured?
3. Are edge cases handled?
4. Is error handling appropriate?

Score: 1-5 (1=poor, 5=excellent)
Reasoning: [explanation]
```

### 3. Human Grader

### 3. Human Grader
### 3. 人工评分器

Flag for manual review:
标记为人工审查：

```markdown
[HUMAN REVIEW REQUIRED]
Change: Description of what changed
Reason: Why human review is needed
Risk Level: LOW/MEDIUM/HIGH
```

## Metrics

## Metrics
## 指标

### pass@k

"At least one success in k attempts"
"在k次尝试中至少一次成功"

- pass@1: First attempt success rate
  pass@1: 首次尝试成功率
- pass@3: Success within 3 attempts
  pass@3: 3次尝试内成功
- Typical target: pass@3 > 90%
  典型目标：pass@3 > 90%

### pass^k

"All k trials succeed"
"所有k次试验都成功"

- Higher bar for reliability
  更高的可靠性标准
- pass^3: 3 consecutive successes
  pass^3: 连续3次成功
- Use for critical paths
  用于关键路径

## Eval Workflow

## Eval Workflow
## 评估工作流

### 1. Define (Before Coding)

### 1. Define (Before Coding)
### 1. 定义（编码之前）

```markdown
## EVAL DEFINITION: feature-xyz

### Capability Evals
1. Can create new user account
2. Can validate email format
3. Can hash password securely

### Regression Evals
1. Existing login still works
2. Session management unchanged
3. Logout flow intact

### Success Metrics
- pass@3 > 90% for capability evals
- pass^3 = 100% for regression evals
```

### 2. Implement

### 2. Implement
### 2. 实现

Write code to pass the defined evals.
编写代码以通过定义的评估。

### 3. Evaluate

### 3. Evaluate
### 3. 评估

```bash
# Run capability evals
[Run each capability eval, record PASS/FAIL]

# Run regression evals
npm test -- --testPathPattern="existing"

# Generate report
```

### 4. Report

### 4. Report
### 4. 报告

```markdown
EVAL REPORT: feature-xyz
========================

Capability Evals:
  create-user:     PASS (pass@1)
  validate-email:  PASS (pass@2)
  hash-password:   PASS (pass@1)
  Overall:         3/3 passed

Regression Evals:
  login-flow:      PASS
  session-mgmt:    PASS
  logout-flow:     PASS
  Overall:         3/3 passed

Metrics:
  pass@1: 67% (2/3)
  pass@3: 100% (3/3)

Status: READY FOR REVIEW
```

## Integration Patterns

## Integration Patterns
## 集成模式

### Pre-Implementation
### Pre-Implementation
### 实现前

```
/eval define feature-name
```
Creates eval definition file at `.claude/evals/feature-name.md`
在`.claude/evals/feature-name.md`创建评估定义文件

### During Implementation
### During Implementation
### 实现期间

```
/eval check feature-name
```
Runs current evals and reports status
运行当前评估并报告状态

### Post-Implementation
### Post-Implementation
### 实现后

```
/eval report feature-name
```
Generates full eval report
生成完整评估报告

## Eval Storage

## Eval Storage
## 评估存储

Store evals in project:
在项目中存储评估：

```
.claude/
  evals/
    feature-xyz.md      # Eval definition
    feature-xyz.log     # Eval run history
    baseline.json       # Regression baselines
```

## Best Practices

## Best Practices
## 最佳实践

1. **Define evals BEFORE coding** - Forces clear thinking about success criteria
   **编码之前定义评估** - 迫使清晰思考成功标准
2. **Run evals frequently** - Catch regressions early
   **频繁运行评估** - 及早发现回归
3. **Track pass@k over time** - Monitor reliability trends
   **持续追踪pass@k** - 监控可靠性趋势
4. **Use code graders when possible** - Deterministic > probabilistic
   **尽可能使用代码评分器** - 确定性 > 概率性
5. **Human review for security** - Never fully automate security checks
   **人工审查安全性** - 永远不要完全自动化安全检查
6. **Keep evals fast** - Slow evals don't get run
   **保持评估快速** - 慢评估不会被运行
7. **Version evals with code** - Evals are first-class artifacts
   **与代码版本化评估** - 评估是一级产物

## Example: Adding Authentication

## Example: Adding Authentication
## 示例：添加认证

```markdown
## EVAL: add-authentication

### Phase 1: Define (10 min)
Capability Evals:
- [ ] User can register with email/password
- [ ] User can login with valid credentials
- [ ] Invalid credentials rejected with proper error
- [ ] Sessions persist across page reloads
- [ ] Logout clears session

Regression Evals:
- [ ] Public routes still accessible
- [ ] API responses unchanged
- [ ] Database schema compatible

### Phase 2: Implement (varies)
[Write code]

### Phase 3: Evaluate
Run: /eval check add-authentication

### Phase 4: Report
EVAL REPORT: add-authentication
==============================
Capability: 5/5 passed (pass@3: 100%)
Regression: 3/3 passed (pass^3: 100%)
Status: SHIP IT
```

## Product Evals (v1.8)

## Product Evals (v1.8)
## 产品评估（v1.8）

Use product evals when behavior quality cannot be captured by unit tests alone.
当行为质量无法仅通过单元测试捕获时，使用产品评估。

### Grader Types

### Grader Types
### 评分器类型

1. Code grader (deterministic assertions)
   代码评分器（确定性断言）
2. Rule grader (regex/schema constraints)
   规则评分器（正则/模式约束）
3. Model grader (LLM-as-judge rubric)
   模型评分器（LLM评判标准）
4. Human grader (manual adjudication for ambiguous outputs)
   人工评分器（模糊输出的手动裁决）

### pass@k Guidance

### pass@k Guidance
### pass@k指导

- `pass@1`: direct reliability
  `pass@1`：直接可靠性
- `pass@3`: practical reliability under controlled retries
  `pass@3`：受控重试下的实际可靠性
- `pass^3`: stability test (all 3 runs must pass)
  `pass^3`：稳定性测试（所有3次运行必须通过）

Recommended thresholds:
推荐阈值：

- Capability evals: pass@3 >= 0.90
  能力评估：pass@3 >= 0.90
- Regression evals: pass^3 = 1.00 for release-critical paths
  回归评估：发布关键路径 pass^3 = 1.00

### Eval Anti-Patterns

### Eval Anti-Patterns
### 评估反模式

- Overfitting prompts to known eval examples
  在已知评估示例上过度拟合提示
- Measuring only happy-path outputs
  仅测量快乐路径输出
- Ignoring cost and latency drift while chasing pass rates
  在追求通过率时忽略成本和延迟漂移
- Allowing flaky graders in release gates
  在发布关卡中允许不稳定的评分器

### Minimal Eval Artifact Layout

### Minimal Eval Artifact Layout
### 最小评估产物布局

- `.claude/evals/<feature>.md` definition
  `.claude/evals/<feature>.md` 定义
- `.claude/evals/<feature>.log` run history
  `.claude/evals/<feature>.log` 运行历史
- `docs/releases/<version>/eval-summary.md` release snapshot
  `docs/releases/<version>/eval-summary.md` 发布快照
