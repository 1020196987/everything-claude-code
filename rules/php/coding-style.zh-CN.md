---
name: PHP Coding Style
description: PHP Coding Style
description: PHP 编码风格规范
paths:
  - "**/*.php"
  - "**/composer.json"
---
# PHP Coding Style
# PHP 编码风格

> This file extends [common/coding-style.md](../common/coding-style.md) with PHP specific content.
> 此文件扩展了 [common/coding-style.md](../common/coding-style.md)，包含 PHP 特定内容。

## Standards
## 标准

- Follow **PSR-12** formatting and naming conventions.
- 遵循 **PSR-12** 格式化和命名约定。
- Prefer `declare(strict_types=1);` in application code.
- 在应用代码中优先使用 `declare(strict_types=1);`。
- Use scalar type hints, return types, and typed properties everywhere new code permits.
- 在新代码允许的地方使用标量类型提示、返回类型和类型化属性。

## Immutability
## 不可变性

- Prefer immutable DTOs and value objects for data crossing service boundaries.
- 优先使用跨服务边界的不可变 DTO 和值对象。
- Use `readonly` properties or immutable constructors for request/response payloads where possible.
- 在可能的情况下，对请求/响应负载使用 `readonly` 属性或不可变构造函数。
- Keep arrays for simple maps; promote business-critical structures into explicit classes.
- 对简单映射保留数组；将业务关键结构提升为显式类。

## Formatting
## 格式化

- Use **PHP-CS-Fixer** or **Laravel Pint** for formatting.
- 使用 **PHP-CS-Fixer** 或 **Laravel Pint** 进行格式化。
- Use **PHPStan** or **Psalm** for static analysis.
- 使用 **PHPStan** 或 **Psalm** 进行静态分析。
- Keep Composer scripts checked in so the same commands run locally and in CI.
- 保留已签入的 Composer 脚本，以便在本地和 CI 中运行相同命令。

## Imports
## 导入

- Add `use` statements for all referenced classes, interfaces, and traits.
- 为所有引用的类、接口和 trait 添加 `use` 语句。
- Avoid relying on the global namespace unless the project explicitly prefers fully qualified names.
- 除非项目明确偏好完全限定名称，否则避免依赖全局命名空间。

## Error Handling
## 错误处理

- Throw exceptions for exceptional states; avoid returning `false`/`null` as hidden error channels in new code.
- 对异常状态抛出异常；避免在新代码中将 `false`/`null` 作为隐藏的错误通道。
- Convert framework/request input into validated DTOs before it reaches domain logic.
- 在框架/请求输入到达领域逻辑之前，将其转换为已验证的 DTO。

## Reference
## 参考

See skill: `backend-patterns` for broader service/repository layering guidance.
参见 skill: `backend-patterns` 了解更广泛的服务/仓储分层指导。
