---
description: Enforce TDD workflow for Go. Write table-driven tests first, then implement. Verify 80%+ coverage with go test -cover.
description-zh: 强制 Go 的 TDD 工作流。先写表驱动测试，然后实现。用 go test -cover 验证 80%+ 覆盖率。
---

# Go TDD Command
# Go TDD 命令

This command enforces test-driven development methodology for Go code using idiomatic Go testing patterns.
此命令使用惯用的 Go 测试模式对 Go 代码强制执行测试驱动开发方法论。

## What This Command Does
## 此命令做什么

1. **Define Types/Interfaces**: Scaffold function signatures first
   **定义类型/接口**：先搭建函数签名
2. **Write Table-Driven Tests**: Create comprehensive test cases (RED)
   **编写表驱动测试**：创建全面的测试用例（RED）
3. **Run Tests**: Verify tests fail for the right reason
   **运行测试**：验证测试失败原因正确
4. **Implement Code**: Write minimal code to pass (GREEN)
   **实现代码**：编写最小代码使测试通过（GREEN）
5. **Refactor**: Improve while keeping tests green
   **重构**：在保持测试通过的同时改进
6. **Check Coverage**: Ensure 80%+ coverage
   **检查覆盖率**：确保 80%+ 覆盖率

## When to Use
## 使用场景

Use `/go-test` when:
使用 `/go-test` 当：
- Implementing new Go functions
  实现新的 Go 函数
- Adding test coverage to existing code
  为现有代码添加测试覆盖率
- Fixing bugs (write failing test first)
  修复 bug（先写失败的测试）
- Building critical business logic
  构建关键业务逻辑
- Learning TDD workflow in Go
  学习 Go 中的 TDD 工作流

## TDD Cycle
## TDD 循环

```
RED     → Write failing table-driven test
GREEN   → Implement minimal code to pass
REFACTOR → Improve code, tests stay green
REPEAT  → Next test case
```

```
RED     → 编写一个会失败的表驱动测试
GREEN   → 实现最小代码使其通过
REFACTOR → 改进代码，测试保持通过
REPEAT  → 下一个测试用例
```

## Related Commands
## 相关命令

- `/go-build` - Fix build errors
  `/go-build` - 修复构建错误
- `/go-review` - Review code after implementation
  `/go-review` - 实现后审查代码
- `/verify` - Run full verification loop
  `/verify` - 运行完整验证循环
