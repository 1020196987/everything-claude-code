---
name: PHP Security
description: PHP Security
description: PHP 安全指南
paths:
  - "**/*.php"
  - "**/composer.lock"
  - "**/composer.json"
---
# PHP Security
# PHP 安全

> This file extends [common/security.md](../common/security.md) with PHP specific content.
> 此文件扩展了 [common/security.md](../common/security.md)，包含 PHP 特定内容。

## Input and Output
## 输入和输出

- Validate request input at the framework boundary (`FormRequest`, Symfony Validator, or explicit DTO validation).
- 在框架边界验证请求输入（`FormRequest`、Symfony Validator 或显式 DTO 验证）。
- Escape output in templates by default; treat raw HTML rendering as an exception that must be justified.
- 默认在模板中转义输出；将原始 HTML 渲染视为需要正当理由的例外。
- Never trust query params, cookies, headers, or uploaded file metadata without validation.
- 永不信任查询参数、cookie、header 或上传文件元数据，而不进行验证。

## Database Safety
## 数据库安全

- Use prepared statements (`PDO`, Doctrine, Eloquent query builder) for all dynamic queries.
- 对所有动态查询使用预处理语句（`PDO`、Doctrine、Eloquent 查询构建器）。
- Avoid string-building SQL in controllers/views.
- 避免在控制器/视图中构建字符串 SQL。
- Scope ORM mass-assignment carefully and whitelist writable fields.
- 仔细限定 ORM 批量分配并白名单化可写字段。

## Secrets and Dependencies
## 密钥和依赖项

- Load secrets from environment variables or a secret manager, never from committed config files.
- 从环境变量或密钥管理器加载密钥，永不从已提交的配置文件加载。
- Run `composer audit` in CI and review new package maintainer trust before adding dependencies.
- 在 CI 中运行 `composer audit`，并在添加依赖项之前审查新包维护者的可信度。
- Pin major versions deliberately and remove abandoned packages quickly.
- 刻意固定主版本，并快速移除已废弃的包。

## Auth and Session Safety
## 认证和会话安全

- Use `password_hash()` / `password_verify()` for password storage.
- 使用 `password_hash()` / `password_verify()` 存储密码。
- Regenerate session identifiers after authentication and privilege changes.
- 在身份验证和权限更改后重新生成会话标识符。
- Enforce CSRF protection on state-changing web requests.
- 对状态更改的 Web 请求强制执行 CSRF 保护。

## Reference
## 参考

See skill: `laravel-security` for Laravel-specific security guidance.
参见 skill: `laravel-security` 了解 Laravel 特定的安全指导。
