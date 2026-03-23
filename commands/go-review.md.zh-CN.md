---
description: Comprehensive Go code review for idiomatic patterns, concurrency safety, error handling, and security. Invokes the go-reviewer agent.
description-zh: 对 Go 代码进行惯用模式、并发安全、错误处理和安全的综合代码审查。调用 go-reviewer agent。
---

# Go Code Review
# Go 代码审查

This command invokes the **go-reviewer** agent for comprehensive Go-specific code review.
此命令调用 **go-reviewer** agent 进行全面的 Go 特定代码审查。

## What This Command Does
## 此命令做什么

1. **Identify Go Changes**: Find modified `.go` files via `git diff`
   **识别 Go 更改**：通过 `git diff` 找到修改的 `.go` 文件
2. **Run Static Analysis**: Execute `go vet`, `staticcheck`, and `golangci-lint`
   **运行静态分析**：执行 `go vet`、`staticcheck` 和 `golangci-lint`
3. **Security Scan**: Check for SQL injection, command injection, race conditions
   **安全扫描**：检查 SQL 注入、命令注入、竞态条件
4. **Concurrency Review**: Analyze goroutine safety, channel usage, mutex patterns
   **并发审查**：分析 goroutine 安全、channel 用法、mutex 模式
5. **Idiomatic Go Check**: Verify code follows Go conventions and best practices
   **惯用 Go 检查**：验证代码遵循 Go 约定和最佳实践
6. **Generate Report**: Categorize issues by severity
   **生成报告**：按严重程度对问题分类

## When to Use
## 使用场景

Use `/go-review` when:
使用 `/go-review` 当：
- After writing or modifying Go code
  编写或修改 Go 代码之后
- Before committing Go changes
  提交 Go 更改之前
- Reviewing pull requests with Go code
  审查包含 Go 代码的 pull requests
- Onboarding to a new Go codebase
  加入新的 Go 代码库
- Learning idiomatic Go patterns
  学习惯用 Go 模式

## Approval Criteria
## 批准标准

| Status | Condition |
|--------|-----------|
| Approve | No CRITICAL or HIGH issues |
| Warning | Only MEDIUM issues (merge with caution) |
| Block | CRITICAL or HIGH issues found |

| 状态 | 条件 |
|-----|-----|
| 批准 | 无 CRITICAL 或 HIGH 问题 |
| 警告 | 只有 MEDIUM 问题（谨慎合并） |
| 阻止 | 发现 CRITICAL 或 HIGH 问题 |
