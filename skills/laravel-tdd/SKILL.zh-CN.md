---
name: laravel-tdd
description: Test-driven development for Laravel with PHPUnit and Pest, factories, database testing, fakes, and coverage targets.
description zh-CN: 使用 PHPUnit 和 Pest 进行 Laravel 测试驱动开发，包括工厂、数据库测试、fake 和覆盖率目标。
origin: ECC
---

# Laravel TDD Workflow

## Laravel TDD Workflow
## Laravel TDD 工作流

Test-driven development for Laravel applications using PHPUnit and Pest with 80%+ coverage (unit + feature).
使用 PHPUnit 和 Pest 对 Laravel 应用程序进行测试驱动开发，单元测试和功能测试覆盖率达 80% 以上。

## When to Use

## When to Use
## 何时使用

- New features or endpoints in Laravel
  - Laravel 中的新功能或端点
- Bug fixes or refactors
  - 错误修复或重构
- Testing Eloquent models, policies, jobs, and notifications
  - 测试 Eloquent 模型、策略、作业和通知
- Prefer Pest for new tests unless the project already standardizes on PHPUnit
  - 除非项目已标准化使用 PHPUnit，否则新测试首选 Pest

## How It Works

## How It Works
## 工作原理

### Red-Green-Refactor Cycle

### Red-Green-Refactor Cycle
### 红-绿-重构循环

1) Write a failing test
   - 编写一个失败的测试
2) Implement the minimal change to pass
   - 实现最小的更改以通过
3) Refactor while keeping tests green
   - 在保持测试通过的情况下重构

### Test Layers

### Test Layers
### 测试层级

- **Unit**: pure PHP classes, value objects, services
  - **单元**：纯 PHP 类、值对象、服务
- **Feature**: HTTP endpoints, auth, validation, policies
  - **功能**：HTTP 端点、认证、验证、策略
- **Integration**: database + queue + external boundaries
  - **集成**：数据库 + 队列 + 外部边界

Choose layers based on scope:
根据范围选择层级：

- Use **Unit** tests for pure business logic and services.
  - 对纯业务逻辑和服务使用**单元**测试
- Use **Feature** tests for HTTP, auth, validation, and response shape.
  - 对 HTTP、认证、验证和响应结构使用**功能**测试
- Use **Integration** tests when validating DB/queues/external services together.
  - 在一起验证 DB/队列/外部服务时使用**集成**测试

### Database Strategy

### Database Strategy
### 数据库策略

- `RefreshDatabase` for most feature/integration tests (runs migrations once per test run, then wraps each test in a transaction when supported; in-memory databases may re-migrate per test)
  - `RefreshDatabase` 用于大多数功能/集成测试（在支持的测试运行中运行一次迁移，然后将每个测试包装在事务中；内存数据库可能每次测试重新迁移）
- `DatabaseTransactions` when the schema is already migrated and you only need per-test rollback
  - 当架构已迁移且只需要每个测试回滚时使用 `DatabaseTransactions`
- `DatabaseMigrations` when you need a full migrate/fresh for every test and can afford the cost
  - 当每个测试需要完整的 migrate/fresh 并且可以承受成本时使用 `DatabaseMigrations`

Use `RefreshDatabase` as the default for tests that touch the database: for databases with transaction support, it runs migrations once per test run (via a static flag) and wraps each test in a transaction; for `:memory:` SQLite or connections without transactions, it migrates before each test. Use `DatabaseTransactions` when the schema is already migrated and you only need per-test rollbacks.
对于涉及数据库的测试，默认使用 `RefreshDatabase`：对于支持事务的数据库，它通过静态标志在每个测试运行中运行一次迁移，并将每个测试包装在事务中；对于 `:memory:` SQLite 或没有事务的连接，它在每个测试之前迁移。当架构已迁移且只需要每个测试回滚时使用 `DatabaseTransactions`。

### Testing Framework Choice

### Testing Framework Choice
### 测试框架选择

- Default to **Pest** for new tests when available.
  - 可用时，新测试默认使用 **Pest**
- Use **PHPUnit** only if the project already standardizes on it or requires PHPUnit-specific tooling.
  - 仅当项目已标准化使用 PHPUnit 或需要 PHPUnit 特定工具时才使用 **PHPUnit**

## Examples

## Examples
## 示例

### PHPUnit Example

### PHPUnit Example
### PHPUnit 示例

```php
use App\Models\User;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

final class ProjectControllerTest extends TestCase
{
    use RefreshDatabase;

    public function test_owner_can_create_project(): void
    {
        $user = User::factory()->create();

        $response = $this->actingAs($user)->postJson('/api/projects', [
            'name' => 'New Project',
        ]);

        $response->assertCreated();
        $this->assertDatabaseHas('projects', ['name' => 'New Project']);
    }
}
```

### Feature Test Example (HTTP Layer)

### Feature Test Example (HTTP Layer)
### 功能测试示例（HTTP 层）

```php
use App\Models\Project;
use App\Models\User;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

final class ProjectIndexTest extends TestCase
{
    use RefreshDatabase;

    public function test_projects_index_returns_paginated_results(): void
    {
        $user = User::factory()->create();
        Project::factory()->count(3)->for($user)->create();

        $response = $this->actingAs($user)->getJson('/api/projects');

        $response->assertOk();
        $response->assertJsonStructure(['success', 'data', 'error', 'meta']);
    }
}
```

### Pest Example

### Pest Example
### Pest 示例

```php
use App\Models\User;
use Illuminate\Foundation\Testing\RefreshDatabase;

use function Pest\Laravel\actingAs;
use function Pest\Laravel\assertDatabaseHas;

uses(RefreshDatabase::class);

test('owner can create project', function () {
    $user = User::factory()->create();

    $response = actingAs($user)->postJson('/api/projects', [
        'name' => 'New Project',
    ]);

    $response->assertCreated();
    assertDatabaseHas('projects', ['name' => 'New Project']);
});
```

### Feature Test Pest Example (HTTP Layer)

### Feature Test Pest Example (HTTP Layer)
### 功能测试 Pest 示例（HTTP 层）

```php
use App\Models\Project;
use App\Models\User;
use Illuminate\Foundation\Testing\RefreshDatabase;

use function Pest\Laravel\actingAs;

uses(RefreshDatabase::class);

test('projects index returns paginated results', function () {
    $user = User::factory()->create();
    Project::factory()->count(3)->for($user)->create();

    $response = actingAs($user)->getJson('/api/projects');

    $response->assertOk();
    $response->assertJsonStructure(['success', 'data', 'error', 'meta']);
});
```

### Factories and States

### Factories and States
### 工厂和状态

- Use factories for test data
  - 使用工厂生成测试数据
- Define states for edge cases (archived, admin, trial)
  - 为边界情况定义状态（已归档、管理员、试用）

```php
$user = User::factory()->state(['role' => 'admin'])->create();
```

### Database Testing

### Database Testing
### 数据库测试

- Use `RefreshDatabase` for clean state
  - 使用 `RefreshDatabase` 保持干净状态
- Keep tests isolated and deterministic
  - 保持测试隔离和确定性
- Prefer `assertDatabaseHas` over manual queries
  - 优先使用 `assertDatabaseHas` 而不是手动查询

### Persistence Test Example

### Persistence Test Example
### 持久化测试示例

```php
use App\Models\Project;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

final class ProjectRepositoryTest extends TestCase
{
    use RefreshDatabase;

    public function test_project_can_be_retrieved_by_slug(): void
    {
        $project = Project::factory()->create(['slug' => 'alpha']);

        $found = Project::query()->where('slug', 'alpha')->firstOrFail();

        $this->assertSame($project->id, $found->id);
    }
}
```

### Fakes for Side Effects

### Fakes for Side Effects
### 副作用的 Fake

- `Bus::fake()` for jobs
  - `Bus::fake()` 用于作业
- `Queue::fake()` for queued work
  - `Queue::fake()` 用于队列工作
- `Mail::fake()` and `Notification::fake()` for notifications
  - `Mail::fake()` 和 `Notification::fake()` 用于通知
- `Event::fake()` for domain events
  - `Event::fake()` 用于领域事件

```php
use Illuminate\Support\Facades\Queue;

Queue::fake();

dispatch(new SendOrderConfirmation($order->id));

Queue::assertPushed(SendOrderConfirmation::class);
```

```php
use Illuminate\Support\Facades\Notification;

Notification::fake();

$user->notify(new InvoiceReady($invoice));

Notification::assertSentTo($user, InvoiceReady::class);
```

### Auth Testing (Sanctum)

### Auth Testing (Sanctum)
### 认证测试（Sanctum）

```php
use Laravel\Sanctum\Sanctum;

Sanctum::actingAs($user);

$response = $this->getJson('/api/projects');
$response->assertOk();
```

### HTTP and External Services

### HTTP and External Services
### HTTP 和外部服务

- Use `Http::fake()` to isolate external APIs
  - 使用 `Http::fake()` 隔离外部 API
- Assert outbound payloads with `Http::assertSent()`
  - 使用 `Http::assertSent()` 断言出站载荷

### Coverage Targets

### Coverage Targets
### 覆盖率目标

- Enforce 80%+ coverage for unit + feature tests
  - 单元测试和功能测试强制 80% 以上覆盖率
- Use `pcov` or `XDEBUG_MODE=coverage` in CI
  - 在 CI 中使用 `pcov` 或 `XDEBUG_MODE=coverage`

### Test Commands

### Test Commands
### 测试命令

- `php artisan test`
- `vendor/bin/phpunit`
- `vendor/bin/pest`

### Test Configuration

### Test Configuration
### 测试配置

- Use `phpunit.xml` to set `DB_CONNECTION=sqlite` and `DB_DATABASE=:memory:` for fast tests
  - 使用 `phpunit.xml` 设置 `DB_CONNECTION=sqlite` 和 `DB_DATABASE=:memory:` 以加快测试
- Keep separate env for tests to avoid touching dev/prod data
  - 保持独立的测试环境以避免影响开发/生产数据

### Authorization Tests

### Authorization Tests
### 授权测试

```php
use Illuminate\Support\Facades\Gate;

$this->assertTrue(Gate::forUser($user)->allows('update', $project));
$this->assertFalse(Gate::forUser($otherUser)->allows('update', $project));
```

### Inertia Feature Tests

### Inertia Feature Tests
### Inertia 功能测试

When using Inertia.js, assert on the component name and props with the Inertia testing helpers.
使用 Inertia.js 时，使用 Inertia 测试助手对组件名称和 props 进行断言。

```php
use App\Models\User;
use Inertia\Testing\AssertableInertia;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

final class DashboardInertiaTest extends TestCase
{
    use RefreshDatabase;

    public function test_dashboard_inertia_props(): void
    {
        $user = User::factory()->create();

        $response = $this->actingAs($user)->get('/dashboard');

        $response->assertOk();
        $response->assertInertia(fn (AssertableInertia $page) => $page
            ->component('Dashboard')
            ->where('user.id', $user->id)
            ->has('projects')
        );
    }
}
```

Prefer `assertInertia` over raw JSON assertions to keep tests aligned with Inertia responses.
优先使用 `assertInertia` 而不是原始 JSON 断言，以保持测试与 Inertia 响应一致。
