---
name: Testing
description: Testing requirements and TDD workflow
description: 测试要求和 TDD 工作流
---

# Testing Requirements
# 测试要求

## Minimum Test Coverage: 80%
## 最低测试覆盖率：80%

Test Types (ALL required):
测试类型（全部必需）：

1. **Unit Tests** - Individual functions, utilities, components
   **单元测试** - 独立函数、工具类、组件
2. **Integration Tests** - API endpoints, database operations
   **集成测试** - API 端点、数据库操作
3. **E2E Tests** - Critical user flows (framework chosen per language)
   **端到端测试** - 关键用户流程（框架按语言选择）

## Test-Driven Development
## 测试驱动开发

MANDATORY workflow:
强制工作流：

1. Write test first (RED)
   先写测试（RED）
2. Run test - it should FAIL
   运行测试 - 应该失败
3. Write minimal implementation (GREEN)
   编写最小实现（GREEN）
4. Run test - it should PASS
   运行测试 - 应该通过
5. Refactor (IMPROVE)
   重构（IMPROVE）
6. Verify coverage (80%+)
   验证覆盖率（80%+）

## Troubleshooting Test Failures
## 测试故障排除

1. Use **tdd-guide** agent
   使用 **tdd-guide** agent
2. Check test isolation
   检查测试隔离
3. Verify mocks are correct
   验证 mock 是否正确
4. Fix implementation, not tests (unless tests are wrong)
   修复实现，而非测试（除非测试本身有误）

## Agent Support
## Agent 支持

- **tdd-guide** - Use PROACTIVELY for new features, enforces write-tests-first
  **tdd-guide** - 主动用于新功能，强制先写测试
