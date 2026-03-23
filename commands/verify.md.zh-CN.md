---
description: Run comprehensive verification on current codebase state. Build, types, lint, tests, console.log audit, and git status.
description-zh: 对当前代码库状态运行综合验证。构建、类型检查、lint、测试、console.log 审计和 git 状态。
---

# Verification Command
# 验证命令

Run comprehensive verification on current codebase state.
对当前代码库状态运行综合验证。

## Instructions
## 指令

Execute verification in this exact order:
按此确切顺序执行验证：

1. **Build Check**
   - Run the build command for this project
   - If it fails, report errors and STOP

   **构建检查**
   - 为此项目运行构建命令
   - 如果失败，报告错误并停止

2. **Type Check**
   - Run TypeScript/type checker
   - Report all errors with file:line

   **类型检查**
   - 运行 TypeScript/类型检查器
   - 报告所有错误及 file:line

3. **Lint Check**
   - Run linter
   - Report warnings and errors

   **Lint 检查**
   - 运行 linter
   - 报告警告和错误

4. **Test Suite**
   - Run all tests
   - Report pass/fail count
   - Report coverage percentage

   **测试套件**
   - 运行所有测试
   - 报告通过/失败数量
   - 报告覆盖率百分比

5. **Console.log Audit**
   - Search for console.log in source files
   - Report locations

   **Console.log 审计**
   - 在源文件中搜索 console.log
   - 报告位置

6. **Git Status**
   - Show uncommitted changes
   - Show files modified since last commit

   **Git 状态**
   - 显示未提交的更改
   - 显示自上次提交以来修改的文件

## Output
## 输出

Produce a concise verification report:
生成简洁的验证报告：

```
VERIFICATION: [PASS/FAIL]

Build:    [OK/FAIL]
Types:    [OK/X errors]
Lint:     [OK/X issues]
Tests:    [X/Y passed, Z% coverage]
Secrets:  [OK/X found]
Logs:     [OK/X console.logs]

Ready for PR: [YES/NO]
```

If any critical issues, list them with fix suggestions.
如果有任何关键问题，列出它们并提供修复建议。

## Arguments
## 参数

$ARGUMENTS can be:
- `quick` - Only build + types
  快速验证 - 仅构建 + 类型
- `full` - All checks (default)
  完整验证 - 所有检查（默认）
- `pre-commit` - Checks relevant for commits
  提交前验证 - 与提交相关的检查
- `pre-pr` - Full checks plus security scan
  PR 前验证 - 完整检查加安全扫描
