---
name: laravel-patterns
description: Laravel architecture patterns, routing/controllers, Eloquent ORM, service layers, queues, events, caching, and API resources for production apps.
description zh-CN: Laravel 架构模式，包括路由/控制器、Eloquent ORM、服务层、队列、事件、缓存和 API 资源，适用于生产应用。
origin: ECC
---

# Laravel Development Patterns

## Laravel Development Patterns
## Laravel 开发模式

Production-grade Laravel architecture patterns for scalable, maintainable applications.
适用于可扩展、可维护应用程序的生产级 Laravel 架构模式。

## When to Use

## When to Use
## 何时使用

- Building Laravel web applications or APIs
  - 构建 Laravel Web 应用程序或 API
- Structuring controllers, services, and domain logic
  - 构建控制器、服务和领域逻辑的结构
- Working with Eloquent models and relationships
  - 使用 Eloquent 模型和关系
- Designing APIs with resources and pagination
  - 使用资源和分页设计 API
- Adding queues, events, caching, and background jobs
  - 添加队列、事件、缓存和后台作业

## How It Works

## How It Works
## 工作原理

- Structure the app around clear boundaries (controllers -> services/actions -> models).
  - 在清晰的边界周围构建应用（控制器 -> 服务/动作 -> 模型）
- Use explicit bindings and scoped bindings to keep routing predictable; still enforce authorization for access control.
  - 使用显式绑定和作用域绑定来保持路由的可预测性；仍然需要执行授权以进行访问控制
- Favor typed models, casts, and scopes to keep domain logic consistent.
  - 使用类型化模型、转换和作用域来保持领域逻辑的一致性
- Keep IO-heavy work in queues and cache expensive reads.
  - 将 IO 密集型工作放在队列中，并缓存昂贵的读取操作
- Centralize config in `config/*` and keep environments explicit.
  - 将配置集中在 `config/*` 中，并保持环境显式化

## Examples

## Examples
## 示例

### Project Structure

### Project Structure
### 项目结构

Use a conventional Laravel layout with clear layer boundaries (HTTP, services/actions, models).
使用带有清晰层边界的常规 Laravel 布局（HTTP、服务/动作、模型）。

### Recommended Layout

### Recommended Layout
### 推荐布局

```
app/
├── Actions/            # Single-purpose use cases
│                       # 单一用途用例
├── Console/
├── Events/
├── Exceptions/
├── Http/
│   ├── Controllers/
│   ├── Middleware/
│   ├── Requests/       # Form request validation
│   │                   # 表单请求验证
│   └── Resources/      # API resources
│                       # API 资源
├── Jobs/
├── Models/
├── Policies/
├── Providers/
├── Services/           # Coordinating domain services
│                       # 协调领域服务
└── Support/
config/
database/
├── factories/
├── migrations/
└── seeders/
resources/
├── views/
└── lang/
routes/
├── api.php
├── web.php
└── console.php
```

### Controllers -> Services -> Actions

### Controllers -> Services -> Actions
### 控制器 -> 服务 -> 动作

Keep controllers thin. Put orchestration in services and single-purpose logic in actions.
保持控制器精简。将编排逻辑放在服务中，单一用途逻辑放在动作中。

```php
final class CreateOrderAction
{
    public function __construct(private OrderRepository $orders) {}

    public function handle(CreateOrderData $data): Order
    {
        return $this->orders->create($data);
    }
}

final class OrdersController extends Controller
{
    public function __construct(private CreateOrderAction $createOrder) {}

    public function store(StoreOrderRequest $request): JsonResponse
    {
        $order = $this->createOrder->handle($request->toDto());

        return response()->json([
            'success' => true,
            'data' => OrderResource::make($order),
            'error' => null,
            'meta' => null,
        ], 201);
    }
}
```

### Routing and Controllers

### Routing and Controllers
### 路由和控制器

Prefer route-model binding and resource controllers for clarity.
为清晰起见，首选路由模型绑定和资源控制器。

```php
use Illuminate\Support\Facades\Route;

Route::middleware('auth:sanctum')->group(function () {
    Route::apiResource('projects', ProjectController::class);
});
```

### Route Model Binding (Scoped)

### Route Model Binding (Scoped)
### 路由模型绑定（作用域）

Use scoped bindings to prevent cross-tenant access.
使用作用域绑定来防止跨租户访问。

```php
Route::scopeBindings()->group(function () {
    Route::get('/accounts/{account}/projects/{project}', [ProjectController::class, 'show']);
});
```

### Nested Routes and Binding Names

### Nested Routes and Binding Names
### 嵌套路由和绑定名称

- Keep prefixes and paths consistent to avoid double nesting (e.g., `conversation` vs `conversations`).
  - 保持前缀和路径一致以避免双重嵌套（例如 `conversation` vs `conversations`）
- Use a single parameter name that matches the bound model (e.g., `{conversation}` for `Conversation`).
  - 使用与绑定模型匹配的单一参数名称（例如 `Conversation` 使用 `{conversation}`）
- Prefer scoped bindings when nesting to enforce parent-child relationships.
  - 嵌套时首选作用域绑定以强制执行父子关系

```php
use App\Http\Controllers\Api\ConversationController;
use App\Http\Controllers\Api\MessageController;
use Illuminate\Support\Facades\Route;

Route::middleware('auth:sanctum')->prefix('conversations')->group(function () {
    Route::post('/', [ConversationController::class, 'store'])->name('conversations.store');

    Route::scopeBindings()->group(function () {
        Route::get('/{conversation}', [ConversationController::class, 'show'])
            ->name('conversations.show');

        Route::post('/{conversation}/messages', [MessageController::class, 'store'])
            ->name('conversation-messages.store');

        Route::get('/{conversation}/messages/{message}', [MessageController::class, 'show'])
            ->name('conversation-messages.show');
    });
});
```

If you want a parameter to resolve to a different model class, define explicit binding. For custom binding logic, use `Route::bind()` or implement `resolveRouteBinding()` on the model.
如果希望参数解析为不同的模型类，请定义显式绑定。对于自定义绑定逻辑，请使用 `Route::bind()` 或在模型上实现 `resolveRouteBinding()`。

```php
use App\Models\AiConversation;
use Illuminate\Support\Facades\Route;

Route::model('conversation', AiConversation::class);
```

### Service Container Bindings

### Service Container Bindings
### 服务容器绑定

Bind interfaces to implementations in a service provider for clear dependency wiring.
在服务提供者中将接口绑定到实现，以实现清晰的依赖关系接线。

```php
use App\Repositories\EloquentOrderRepository;
use App\Repositories\OrderRepository;
use Illuminate\Support\ServiceProvider;

final class AppServiceProvider extends ServiceProvider
{
    public function register(): void
    {
        $this->app->bind(OrderRepository::class, EloquentOrderRepository::class);
    }
}
```

### Eloquent Model Patterns

### Eloquent Model Patterns
### Eloquent 模型模式

#### Model Configuration

#### Model Configuration
#### 模型配置

```php
final class Project extends Model
{
    use HasFactory;

    protected $fillable = ['name', 'owner_id', 'status'];

    protected $casts = [
        'status' => ProjectStatus::class,
        'archived_at' => 'datetime',
    ];

    public function owner(): BelongsTo
    {
        return $this->belongsTo(User::class, 'owner_id');
    }

    public function scopeActive(Builder $query): Builder
    {
        return $query->whereNull('archived_at');
    }
}
```

### Custom Casts and Value Objects

### Custom Casts and Value Objects
### 自定义转换和值对象

Use enums or value objects for strict typing.
使用枚举或值对象进行严格类型检查。

```php
use Illuminate\Database\Eloquent\Casts\Attribute;

protected $casts = [
    'status' => ProjectStatus::class,
];
```

```php
protected function budgetCents(): Attribute
{
    return Attribute::make(
        get: fn (int $value) => Money::fromCents($value),
        set: fn (Money $money) => $money->toCents(),
    );
}
```

### Eager Loading to Avoid N+1

### Eager Loading to Avoid N+1
### 预加载以避免 N+1 问题

```php
$orders = Order::query()
    ->with(['customer', 'items.product'])
    ->latest()
    ->paginate(25);
```

### Query Objects for Complex Filters

### Query Objects for Complex Filters
### 复杂过滤的查询对象

```php
final class ProjectQuery
{
    public function __construct(private Builder $query) {}

    public function ownedBy(int $userId): self
    {
        $query = clone $this->query;

        return new self($query->where('owner_id', $userId));
    }

    public function active(): self
    {
        $query = clone $this->query;

        return new self($query->whereNull('archived_at'));
    }

    public function builder(): Builder
    {
        return $this->query;
    }
}
```

### Global Scopes and Soft Deletes

### Global Scopes and Soft Deletes
### 全局作用域和软删除

Use global scopes for default filtering and `SoftDeletes` for recoverable records.
Use either a global scope or a named scope for the same filter, not both, unless you intend layered behavior.
使用全局作用域进行默认过滤，使用 `SoftDeletes` 进行可恢复的记录。
对同一过滤器使用全局作用域或命名作用域之一，而不是两者兼用，除非你需要分层行为。

```php
use Illuminate\Database\Eloquent\SoftDeletes;
use Illuminate\Database\Eloquent\Builder;

final class Project extends Model
{
    use SoftDeletes;

    protected static function booted(): void
    {
        static::addGlobalScope('active', function (Builder $builder): void {
            $builder->whereNull('archived_at');
        });
    }
}
```

### Query Scopes for Reusable Filters

### Query Scopes for Reusable Filters
### 可重用过滤的查询作用域

```php
use Illuminate\Database\Eloquent\Builder;

final class Project extends Model
{
    public function scopeOwnedBy(Builder $query, int $userId): Builder
    {
        return $query->where('owner_id', $userId);
    }
}

// In service, repository etc.
// 在服务、仓库等中
$projects = Project::ownedBy($user->id)->get();
```

### Transactions for Multi-Step Updates

### Transactions for Multi-Step Updates
### 多步骤更新的事务

```php
use Illuminate\Support\Facades\DB;

DB::transaction(function (): void {
    $order->update(['status' => 'paid']);
    $order->items()->update(['paid_at' => now()]);
});
```

### Migrations

### Migrations
### 迁移

#### Naming Convention

#### Naming Convention
#### 命名约定

- File names use timestamps: `YYYY_MM_DD_HHMMSS_create_users_table.php`
  - 文件名使用时间戳：`YYYY_MM_DD_HHMMSS_create_users_table.php`
- Migrations use anonymous classes (no named class); the filename communicates intent
  - 迁移使用匿名类（无命名类）；文件名传达意图
- Table names are `snake_case` and plural by default
  - 表名默认使用 `snake_case` 且为复数形式

#### Example Migration

#### Example Migration
#### 迁移示例

```php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('orders', function (Blueprint $table): void {
            $table->id();
            $table->foreignId('customer_id')->constrained()->cascadeOnDelete();
            $table->string('status', 32)->index();
            $table->unsignedInteger('total_cents');
            $table->timestamps();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('orders');
    }
};
```

### Form Requests and Validation

### Form Requests and Validation
### 表单请求和验证

Keep validation in form requests and transform inputs to DTOs.
将验证逻辑放在表单请求中，并将输入转换为 DTO。

```php
use App\Models\Order;

final class StoreOrderRequest extends FormRequest
{
    public function authorize(): bool
    {
        return $this->user()?->can('create', Order::class) ?? false;
    }

    public function rules(): array
    {
        return [
            'customer_id' => ['required', 'integer', 'exists:customers,id'],
            'items' => ['required', 'array', 'min:1'],
            'items.*.sku' => ['required', 'string'],
            'items.*.quantity' => ['required', 'integer', 'min:1'],
        ];
    }

    public function toDto(): CreateOrderData
    {
        return new CreateOrderData(
            customerId: (int) $this->validated('customer_id'),
            items: $this->validated('items'),
        );
    }
}
```

### API Resources

### API Resources
### API 资源

Keep API responses consistent with resources and pagination.
保持 API 响应与资源和分页一致。

```php
$projects = Project::query()->active()->paginate(25);

return response()->json([
    'success' => true,
    'data' => ProjectResource::collection($projects->items()),
    'error' => null,
    'meta' => [
        'page' => $projects->currentPage(),
        'per_page' => $projects->perPage(),
        'total' => $projects->total(),
    ],
]);
```

### Events, Jobs, and Queues

### Events, Jobs, and Queues
### 事件、作业和队列

- Emit domain events for side effects (emails, analytics)
  - 发出领域事件以产生副作用（邮件、分析）
- Use queued jobs for slow work (reports, exports, webhooks)
  - 使用队列作业处理慢速工作（报告、导出、webhooks）
- Prefer idempotent handlers with retries and backoff
  - 首选具有重试和退避的幂等处理程序

### Caching

### Caching
### 缓存

- Cache read-heavy endpoints and expensive queries
  - 缓存读密集型端点和昂贵查询
- Invalidate caches on model events (created/updated/deleted)
  - 在模型事件（创建/更新/删除）时使缓存失效
- Use tags when caching related data for easy invalidation
  - 缓存相关数据时使用标签以便轻松失效

### Configuration and Environments

### Configuration and Environments
### 配置和环境

- Keep secrets in `.env` and config in `config/*.php`
  - 将密钥保留在 `.env` 中，配置放在 `config/*.php` 中
- Use per-environment config overrides and `config:cache` in production
  - 使用按环境配置覆盖，在生产环境中使用 `config:cache`
