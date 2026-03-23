---
description: Enforce test-driven development workflow. Scaffold interfaces, generate tests FIRST, then implement minimal code to pass. Ensure 80%+ coverage.
description-zh: 强制测试驱动开发工作流。先搭建接口，生成测试，然后实现最少的通过代码。确保 80%+ 覆盖率。
---

# TDD Command
# TDD 命令

This command invokes the **tdd-guide** agent to enforce test-driven development methodology.
此命令调用 **tdd-guide** agent 强制执行测试驱动开发方法论。

## What This Command Does
## 此命令做什么

1. **Scaffold Interfaces** - Define types/interfaces first
   **搭建接口** - 先定义类型/接口
2. **Generate Tests First** - Write failing tests (RED)
   **先生成测试** - 编写会失败的测试（RED）
3. **Implement Minimal Code** - Write just enough to pass (GREEN)
   **实现最小代码** - 写刚好够通过的代码（GREEN）
4. **Refactor** - Improve code while keeping tests green (REFACTOR)
   **重构** - 在保持测试通过的同时改进代码（REFACTOR）
5. **Verify Coverage** - Ensure 80%+ test coverage
   **验证覆盖率** - 确保 80%+ 测试覆盖率

## When to Use
## 使用场景

Use `/tdd` when:
- Implementing new features
- Adding new functions/components
- Fixing bugs (write test that reproduces bug first)
- Refactoring existing code
- Building critical business logic

使用 `/tdd` 当：
- 实现新功能
- 添加新函数/组件
- 修复 bug（先写能复现 bug 的测试）
- 重构现有代码
- 构建关键业务逻辑

## How It Works
## 工作原理

The tdd-guide agent will:

1. **Define interfaces** for inputs/outputs
   **定义接口**，用于输入/输出
2. **Write tests that will FAIL** (because code doesn't exist yet)
   **编写会失败的测试**（因为代码尚不存在）
3. **Run tests** and verify they fail for the right reason
   **运行测试**，验证失败原因正确
4. **Write minimal implementation** to make tests pass
   **编写最小实现**，使测试通过
5. **Run tests** and verify they pass
   **运行测试**，验证它们通过
6. **Refactor** code while keeping tests green
   **重构**代码，同时保持测试通过
7. **Check coverage** and add more tests if below 80%
   **检查覆盖率**，低于 80% 则添加更多测试

## TDD Cycle
## TDD 循环

```
RED → GREEN → REFACTOR → REPEAT

RED:      Write a failing test
GREEN:    Write minimal code to pass
REFACTOR: Improve code, keep tests passing
REPEAT:   Next feature/scenario
```

```
RED → GREEN → REFACTOR → REPEAT

RED:      编写一个会失败的测试
GREEN:    编写最小代码使其通过
REFACTOR: 改进代码，保持测试通过
REPEAT:   下一个功能/场景
```

## TDD Best Practices
## TDD 最佳实践

**DO:**
- Write the test FIRST, before any implementation
  先写测试，再实现
- Run tests and verify they FAIL before implementing
  运行测试并验证它们在实现前会失败
- Write minimal code to make tests pass
  编写最小代码使测试通过
- Refactor only after tests are green
  仅在测试通过后重构
- Add edge cases and error scenarios
  添加边界情况和错误场景
- Aim for 80%+ coverage (100% for critical code)
  目标 80%+ 覆盖率（关键代码 100%）

**DON'T:**
- Write implementation before tests
  在测试之前写实现
- Skip running tests after each change
  每次更改后跳过运行测试
- Write too much code at once
  一次写太多代码
- Ignore failing tests
  忽略失败的测试
- Test implementation details (test behavior)
  测试实现细节（测试行为）
- Mock everything (prefer integration tests)
  mock 所有东西（优先集成测试）

## Integration with Other Commands
## 与其他命令的集成

- Use `/plan` first to understand what to build
  先用 `/plan` 了解要构建什么
- Use `/tdd` to implement with tests
  用 `/tdd` 通过测试来实现
- Use `/build-fix` if build errors occur
  遇到构建错误时用 `/build-fix`
- Use `/code-review` to review implementation
  用 `/code-review` 审查实现
- Use `/test-coverage` to verify coverage
  用 `/test-coverage` 验证覆盖率
