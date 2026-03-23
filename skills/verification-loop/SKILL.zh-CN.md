---
name: verification-loop
description: "A comprehensive verification system for Claude Code sessions."
description zh-CN: "Claude Code会话的综合验证系统。"
origin: ECC
---

# Verification Loop Skill

## English

A comprehensive verification system for Claude Code sessions.

## 中文

Claude Code会话的综合验证系统。

## When to Use

## English

Invoke this skill:
- After completing a feature or significant code change
- Before creating a PR
- When you want to ensure quality gates pass
- After refactoring

## 中文

调用此skill：
- 完成功能或重大代码更改后
- 创建PR之前
- 当您想确保质量门通过时
- 重构后

## Verification Phases

## English

### Phase 1: Build Verification

## 中文

## 验证阶段

### 阶段1：构建验证

```bash
# Check if project builds
# 检查项目是否构建成功
npm run build 2>&1 | tail -20
# OR
# 或
pnpm build 2>&1 | tail -20
```

If build fails, STOP and fix before continuing.

如果构建失败，停止并修复后再继续。

### Phase 2: Type Check

## English

## 中文

### 阶段2：类型检查

```bash
# TypeScript projects
# TypeScript项目
npx tsc --noEmit 2>&1 | head -30

# Python projects
# Python项目
pyright . 2>&1 | head -30
```

Report all type errors. Fix critical ones before continuing.

报告所有类型错误。修复关键错误后再继续。

### Phase 3: Lint Check

## English

## 中文

### 阶段3：Lint检查

```bash
# JavaScript/TypeScript
npm run lint 2>&1 | head -30

# Python
ruff check . 2>&1 | head -30
```

### Phase 4: Test Suite

## English

## 中文

### 阶段4：测试套件

```bash
# Run tests with coverage
# 运行带覆盖率的测试
npm run test -- --coverage 2>&1 | tail -50

# Check coverage threshold
# 检查覆盖率阈值
# Target: 80% minimum
# 目标：最低80%
```

Report:
- Total tests: X
- Passed: X
- Failed: X
- Coverage: X%

报告：
- 总测试数：X
- 通过：X
- 失败：X
- 覆盖率：X%

### Phase 5: Security Scan

## English

## 中文

### 阶段5：安全扫描

```bash
# Check for secrets
# 检查密钥
grep -rn "sk-" --include="*.ts" --include="*.js" . 2>/dev/null | head -10
grep -rn "api_key" --include="*.ts" --include="*.js" . 2>/dev/null | head -10

# Check for console.log
# 检查console.log
grep -rn "console.log" --include="*.ts" --include="*.tsx" src/ 2>/dev/null | head -10
```

### Phase 6: Diff Review

## English

## 中文

### 阶段6：Diff审查

```bash
# Show what changed
# 显示更改内容
git diff --stat
git diff HEAD~1 --name-only
```

Review each changed file for:
- Unintended changes
- Missing error handling
- Potential edge cases

审查每个更改的文件：
- 无意的更改
- 缺失的错误处理
- 潜在的边界情况

## Output Format

## English

After running all phases, produce a verification report:

## 中文

## 输出格式

运行所有阶段后，生成验证报告：

```
VERIFICATION REPORT
==================

Build:     [PASS/FAIL]
Types:     [PASS/FAIL] (X errors)
Lint:      [PASS/FAIL] (X warnings)
Tests:     [PASS/FAIL] (X/Y passed, Z% coverage)
Security:  [PASS/FAIL] (X issues)
Diff:      [X files changed]

Overall:   [READY/NOT READY] for PR

Issues to Fix:
1. ...
2. ...

验证报告
==================

构建：     [通过/失败]
类型：     [通过/失败] (X个错误)
Lint：     [通过/失败] (X个警告)
测试：     [通过/失败] (X/Y通过，Z%覆盖率)
安全：     [通过/失败] (X个问题)
Diff：     [X个文件更改]

总体：     [准备就绪/未准备就绪] 用于PR

需要修复的问题：
1. ...
2. ...
```

## Continuous Mode

## English

For long sessions, run verification every 15 minutes or after major changes:

## 中文

## 持续模式

对于长会话，每15分钟或重大更改后运行验证：

```markdown
Set a mental checkpoint:
- After completing each function
- After finishing a component
- Before moving to next task

Run: /verify

设置心理检查点：
- 完成每个函数后
- 完成组件后
- 转向下一个任务前

运行：/verify
```

## Integration with Hooks

## English

This skill complements PostToolUse hooks but provides deeper verification.
Hooks catch issues immediately; this skill provides comprehensive review.

## 中文

## 与钩子集成

此skill补充PostToolUse钩子但提供更深入的验证。
钩子立即捕获问题；此skill提供全面审查。
