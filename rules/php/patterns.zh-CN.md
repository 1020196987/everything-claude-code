---
name: PHP Patterns
description: PHP Patterns
description: PHP 设计模式和架构
paths:
  - "**/*.php"
  - "**/composer.json"
---
# PHP Patterns
# PHP 模式

> This file extends [common/patterns.md](../common/patterns.md) with PHP specific content.
> 此文件扩展了 [common/patterns.md](../common/patterns.md)，包含 PHP 特定内容。

## Thin Controllers, Explicit Services
## 精简控制器、显式服务

- Keep controllers focused on transport: auth, validation, serialization, status codes.
- 让控制器专注于传输：身份验证、验证、序列化、状态码。
- Move business rules into application/domain services that are easy to test without HTTP bootstrapping.
- 将业务规则移入应用/领域服务，以便在不需要 HTTP 引导的情况下进行测试。

## DTOs and Value Objects
## DTO 和值对象

- Replace shape-heavy associative arrays with DTOs for requests, commands, and external API payloads.
- 用 DTO 替换笨重的关联数组，用于请求、命令和外部 API 负载。
- Use value objects for money, identifiers, date ranges, and other constrained concepts.
- 对金钱、标识符、日期范围和其他受限概念使用值对象。

## Dependency Injection
## 依赖注入

- Depend on interfaces or narrow service contracts, not framework globals.
- 依赖接口或窄服务契约，而非框架全局变量。
- Pass collaborators through constructors so services are testable without service-locator lookups.
- 通过构造函数传递协作者，以便服务可以在没有服务定位器查找的情况下进行测试。

## Boundaries
## 边界

- Isolate ORM models from domain decisions when the model layer is doing more than persistence.
- 当模型层做的事情超过持久化时，将 ORM 模型与领域决策隔离开来。
- Wrap third-party SDKs behind small adapters so the rest of the codebase depends on your contract, not theirs.
- 将第三方 SDK 包装在小适配器后面，以便代码库的其余部分依赖你的契约，而非它们的契约。

## Reference
## 参考

See skill: `api-design` for endpoint conventions and response-shape guidance.
参见 skill: `api-design` 了解端点约定和响应形状指导。
See skill: `laravel-patterns` for Laravel-specific architecture guidance.
参见 skill: `laravel-patterns` 了解 Laravel 特定的架构指导。
