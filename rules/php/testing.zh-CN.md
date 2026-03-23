---
name: PHP Testing
description: PHP Testing
description: PHP 测试框架和组织
paths:
  - "**/*.php"
  - "**/phpunit.xml"
  - "**/phpunit.xml.dist"
  - "**/composer.json"
---
# PHP Testing
# PHP 测试

> This file extends [common/testing.md](../common/testing.md) with PHP specific content.
> 此文件扩展了 [common/testing.md](../common/testing.md)，包含 PHP 特定内容。

## Framework
## 框架

Use **PHPUnit** as the default test framework. If **Pest** is configured in the project, prefer Pest for new tests and avoid mixing frameworks.
使用 **PHPUnit** 作为默认测试框架。如果项目中配置了 **Pest**，对新测试优先使用 Pest，避免混合使用框架。

## Coverage
## 覆盖率

```bash
vendor/bin/phpunit --coverage-text
# or
vendor/bin/pest --coverage
vendor/bin/phpunit --coverage-text
# 或
vendor/bin/pest --coverage
```

Prefer **pcov** or **Xdebug** in CI, and keep coverage thresholds in CI rather than as tribal knowledge.
在 CI 中优先使用 **pcov** 或 **Xdebug**，并在 CI 中保持覆盖率阈值，而非作为隐性知识。

## Test Organization
## 测试组织

- Separate fast unit tests from framework/database integration tests.
- 将快速单元测试与框架/数据库集成测试分开。
- Use factory/builders for fixtures instead of large hand-written arrays.
- 使用工厂/构建器创建 fixture，而非大型手写数组。
- Keep HTTP/controller tests focused on transport and validation; move business rules into service-level tests.
- 保持 HTTP/控制器测试专注于传输和验证；将业务规则移入服务级测试。

## Inertia
## Inertia

If the project uses Inertia.js, prefer `assertInertia` with `AssertableInertia` to verify component names and props instead of raw JSON assertions.
如果项目使用 Inertia.js，优先使用 `assertInertia` 配合 `AssertableInertia` 来验证组件名称和 props，而非原始 JSON 断言。

## Reference
## 参考

See skill: `tdd-workflow` for the repo-wide RED -> GREEN -> REFACTOR loop.
参见 skill: `tdd-workflow` 了解仓库范围的 RED -> GREEN -> REFACTOR 循环。
See skill: `laravel-tdd` for Laravel-specific testing patterns (PHPUnit and Pest).
参见 skill: `laravel-tdd` 了解 Laravel 特定的测试模式（PHPUnit 和 Pest）。
