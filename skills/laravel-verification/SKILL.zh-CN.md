---
name: laravel-verification
description: Verification loop for Laravel projects: env checks, linting, static analysis, tests with coverage, security scans, and deployment readiness.
description zh-CN: Laravel 项目验证循环：环境检查、linting、静态分析、带覆盖率的测试、安全扫描和部署就绪检查。
origin: ECC
---

# Laravel Verification Loop

## Laravel Verification Loop
## Laravel 验证循环

Run before PRs, after major changes, and pre-deploy.
在 PR 之前、主要更改之后和预部署时运行。

## When to Use

## When to Use
## 何时使用

- Before opening a pull request for a Laravel project
  - 为 Laravel 项目打开 PR 之前
- After major refactors or dependency upgrades
  - 主要重构或依赖升级之后
- Pre-deployment verification for staging or production
  - 预部署验证（预发布或生产环境）
- Running full lint -> test -> security -> deploy readiness pipeline
  - 运行完整的 lint -> test -> security -> deploy 就绪管道

## How It Works

## How It Works
## 工作原理

- Run phases sequentially from environment checks through deployment readiness so each layer builds on the last.
  - 按顺序从环境检查到部署就绪运行各阶段，使每一层都建立在前一层的基础上
- Environment and Composer checks gate everything else; stop immediately if they fail.
  - 环境和 Composer 检查是所有其他检查的前提；失败时立即停止
- Linting/static analysis should be clean before running full tests and coverage.
  - 在运行完整测试和覆盖率之前，linting/静态分析应该通过
- Security and migration reviews happen after tests so you verify behavior before data or release steps.
  - 安全和迁移审查发生在测试之后，这样你可以在数据或发布步骤之前验证行为
- Build/deploy readiness and queue/scheduler checks are final gates; any failure blocks release.
  - 构建/部署就绪和队列/调度程序检查是最终关卡；任何失败都会阻止发布

## Phase 1: Environment Checks

## Phase 1: Environment Checks
## 阶段 1：环境检查

```bash
php -v
composer --version
php artisan --version
```

- Verify `.env` is present and required keys exist
  - 验证 `.env` 存在且包含必需的键
- Confirm `APP_DEBUG=false` for production environments
  - 确认生产环境的 `APP_DEBUG=false`
- Confirm `APP_ENV` matches the target deployment (`production`, `staging`)
  - 确认 `APP_ENV` 与目标部署环境匹配（`production`、`staging`）

If using Laravel Sail locally:
如果本地使用 Laravel Sail：

```bash
./vendor/bin/sail php -v
./vendor/bin/sail artisan --version
```

## Phase 1.5: Composer and Autoload

## Phase 1.5: Composer and Autoload
## 阶段 1.5：Composer 和自动加载

```bash
composer validate
composer dump-autoload -o
```

## Phase 2: Linting and Static Analysis

## Phase 2: Linting and Static Analysis
## 阶段 2：Linting 和静态分析

```bash
vendor/bin/pint --test
vendor/bin/phpstan analyse
```

If your project uses Psalm instead of PHPStan:
如果你的项目使用 Psalm 而不是 PHPStan：

```bash
vendor/bin/psalm
```

## Phase 3: Tests and Coverage

## Phase 3: Tests and Coverage
## 阶段 3：测试和覆盖率

```bash
php artisan test
```

Coverage (CI):
覆盖率（CI）：

```bash
XDEBUG_MODE=coverage php artisan test --coverage
```

CI example (format -> static analysis -> tests):
CI 示例（格式 -> 静态分析 -> 测试）：

```bash
vendor/bin/pint --test
vendor/bin/phpstan analyse
XDEBUG_MODE=coverage php artisan test --coverage
```

## Phase 4: Security and Dependency Checks

## Phase 4: Security and Dependency Checks
## 阶段 4：安全和依赖检查

```bash
composer audit
```

## Phase 5: Database and Migrations

## Phase 5: Database and Migrations
## 阶段 5：数据库和迁移

```bash
php artisan migrate --pretend
php artisan migrate:status
```

- Review destructive migrations carefully
  - 仔细审查破坏性迁移
- Ensure migration filenames follow `Y_m_d_His_*` (e.g., `2025_03_14_154210_create_orders_table.php`) and describe the change clearly
  - 确保迁移文件名遵循 `Y_m_d_His_*`（例如 `2025_03_14_154210_create_orders_table.php`）并清楚地描述更改
- Ensure rollbacks are possible
  - 确保回滚是可行的
- Verify `down()` methods and avoid irreversible data loss without explicit backups
  - 验证 `down()` 方法，避免在没有明确备份的情况下造成不可逆的数据丢失

## Phase 6: Build and Deployment Readiness

## Phase 6: Build and Deployment Readiness
## 阶段 6：构建和部署就绪

```bash
php artisan optimize:clear
php artisan config:cache
php artisan route:cache
php artisan view:cache
```

- Ensure cache warmups succeed in production configuration
  - 确保缓存在生产配置中成功预热
- Verify queue workers and scheduler are configured
  - 验证队列工作进程和调度程序已配置
- Confirm `storage/` and `bootstrap/cache/` are writable in the target environment
  - 确认 `storage/` 和 `bootstrap/cache/` 在目标环境中可写

## Phase 7: Queue and Scheduler Checks

## Phase 7: Queue and Scheduler Checks
## 阶段 7：队列和调度程序检查

```bash
php artisan schedule:list
php artisan queue:failed
```

If Horizon is used:
如果使用 Horizon：

```bash
php artisan horizon:status
```

If `queue:monitor` is available, use it to check backlog without processing jobs:
如果 `queue:monitor` 可用，使用它检查积压而不处理作业：

```bash
php artisan queue:monitor default --max=100
```

Active verification (staging only): dispatch a no-op job to a dedicated queue and run a single worker to process it (ensure a non-`sync` queue connection is configured).
主动验证（仅限预发布环境）：将一个无操作作业分派到专用队列并运行单个工作进程来处理它（确保配置了非 `sync` 队列连接）。

```bash
php artisan tinker --execute="dispatch((new App\\Jobs\\QueueHealthcheck())->onQueue('healthcheck'))"
php artisan queue:work --once --queue=healthcheck
```

Verify the job produced the expected side effect (log entry, healthcheck table row, or metric).
验证作业产生了预期的副作用（日志条目、healthcheck 表行或指标）。

Only run this on non-production environments where processing a test job is safe.
仅在处理测试作业安全的非生产环境中运行此操作。

## Examples

## Examples
## 示例

Minimal flow:
最小流程：

```bash
php -v
composer --version
php artisan --version
composer validate
vendor/bin/pint --test
vendor/bin/phpstan analyse
php artisan test
composer audit
php artisan migrate --pretend
php artisan config:cache
php artisan queue:failed
```

CI-style pipeline:
CI 风格管道：

```bash
composer validate
composer dump-autoload -o
vendor/bin/pint --test
vendor/bin/phpstan analyse
XDEBUG_MODE=coverage php artisan test --coverage
composer audit
php artisan migrate --pretend
php artisan optimize:clear
php artisan config:cache
php artisan route:cache
php artisan view:cache
php artisan schedule:list
```
