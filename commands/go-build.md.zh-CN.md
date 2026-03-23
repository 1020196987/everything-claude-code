---
description: Fix Go build errors, go vet warnings, and linter issues incrementally. Invokes the go-build-resolver agent for minimal, surgical fixes.
description-zh: 增量修复 Go 构建错误、go vet 警告和 linter 问题。调用 go-build-resolver agent 进行最小的、精确的修复。
---

# Go Build and Fix
# Go 构建与修复

This command invokes the **go-build-resolver** agent to incrementally fix Go build errors with minimal changes.
此命令调用 **go-build-resolver** agent 以最小的更改增量修复 Go 构建错误。

## What This Command Does
## 此命令做什么

1. **Run Diagnostics**: Execute `go build`, `go vet`, `staticcheck`
   **运行诊断**：执行 `go build`、`go vet`、`staticcheck`
2. **Parse Errors**: Group by file and sort by severity
   **解析错误**：按文件分组并按严重程度排序
3. **Fix Incrementally**: One error at a time
   **增量修复**：一次一个错误
4. **Verify Each Fix**: Re-run build after each change
   **验证每次修复**：每次更改后重新运行构建
5. **Report Summary**: Show what was fixed and what remains
   **报告总结**：显示已修复的内容和剩余内容

## When to Use
## 使用场景

Use `/go-build` when:
使用 `/go-build` 当：
- `go build ./...` fails with errors
  `go build ./...` 因错误失败
- `go vet ./...` reports issues
  `go vet ./...` 报告问题
- `golangci-lint run` shows warnings
  `golangci-lint run` 显示警告
- Module dependencies are broken
  模块依赖损坏
- After pulling changes that break the build
  拉取破坏构建的更改后

## Related Commands
## 相关命令

- `/go-test` - Run tests after build succeeds
  `/go-test` - 构建成功后运行测试
- `/go-review` - Review code quality
  `/go-review` - 审查代码质量
- `/verify` - Full verification loop
  `/verify` - 完整验证循环
