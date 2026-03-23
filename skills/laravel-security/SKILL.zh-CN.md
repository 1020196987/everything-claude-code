---
name: laravel-security
description: Laravel security best practices for authn/authz, validation, CSRF, mass assignment, file uploads, secrets, rate limiting, and secure deployment.
description zh-CN: Laravel 安全最佳实践，涵盖认证/授权、验证、CSRF、大量赋值、文件上传、密钥、速率限制和安全部署。
origin: ECC
---

# Laravel Security Best Practices

## Laravel Security Best Practices
## Laravel 安全最佳实践

Comprehensive security guidance for Laravel applications to protect against common vulnerabilities.
保护 Laravel 应用程序免受常见漏洞的综合安全指南。

## When to Activate

## When to Activate
## 何时激活

- Adding authentication or authorization
  - 添加认证或授权
- Handling user input and file uploads
  - 处理用户输入和文件上传
- Building new API endpoints
  - 构建新的 API 端点
- Managing secrets and environment settings
  - 管理密钥和环境设置
- Hardening production deployments
  - 加固生产部署

## How It Works

## How It Works
## 工作原理

- Middleware provides baseline protections (CSRF via `VerifyCsrfToken`, security headers via `SecurityHeaders`).
  - 中间件提供基线保护（通过 `VerifyCsrfToken` 实现 CSRF，通过 `SecurityHeaders` 实现安全头）
- Guards and policies enforce access control (`auth:sanctum`, `$this->authorize`, policy middleware).
  - 守卫和策略强制执行访问控制（`auth:sanctum`、`$this->authorize`、策略中间件）
- Form Requests validate and shape input (`UploadInvoiceRequest`) before it reaches services.
  - 表单请求在输入到达服务之前验证和塑造输入（`UploadInvoiceRequest`）
- Rate limiting adds abuse protection (`RateLimiter::for('login')`) alongside auth controls.
  - 速率限制在认证控制之外增加滥用保护（`RateLimiter::for('login')`）
- Data safety comes from encrypted casts, mass-assignment guards, and signed routes (`URL::temporarySignedRoute` + `signed` middleware).
  - 数据安全来自加密转换、大量赋值保护和签名路由（`URL::temporarySignedRoute` + `signed` 中间件）

## Core Security Settings

## Core Security Settings
## 核心安全设置

- `APP_DEBUG=false` in production
  - 生产环境中 `APP_DEBUG=false`
- `APP_KEY` must be set and rotated on compromise
  - 必须设置 `APP_KEY`，泄露后必须轮换
- Set `SESSION_SECURE_COOKIE=true` and `SESSION_SAME_SITE=lax` (or `strict` for sensitive apps)
  - 设置 `SESSION_SECURE_COOKIE=true` 和 `SESSION_SAME_SITE=lax`（敏感应用使用 `strict`）
- Configure trusted proxies for correct HTTPS detection
  - 配置受信任的代理以正确检测 HTTPS

## Session and Cookie Hardening

## Session and Cookie Hardening
## 会话和 Cookie 加固

- Set `SESSION_HTTP_ONLY=true` to prevent JavaScript access
  - 设置 `SESSION_HTTP_ONLY=true` 以防止 JavaScript 访问
- Use `SESSION_SAME_SITE=strict` for high-risk flows
  - 对高风险流程使用 `SESSION_SAME_SITE=strict`
- Regenerate sessions on login and privilege changes
  - 在登录和权限变更时重新生成会话

## Authentication and Tokens

## Authentication and Tokens
## 认证和令牌

- Use Laravel Sanctum or Passport for API auth
  - 使用 Laravel Sanctum 或 Passport 进行 API 认证
- Prefer short-lived tokens with refresh flows for sensitive data
  - 对于敏感数据，首选短生命周期令牌和刷新流程
- Revoke tokens on logout and compromised accounts
  - 在注销和账户泄露时撤销令牌

Example route protection:
路由保护示例：

```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Route;

Route::middleware('auth:sanctum')->get('/me', function (Request $request) {
    return $request->user();
});
```

## Password Security

## Password Security
## 密码安全

- Hash passwords with `Hash::make()` and never store plaintext
  - 使用 `Hash::make()` 哈希密码，绝不存储明文
- Use Laravel's password broker for reset flows
  - 使用 Laravel 的密码代理进行重置流程

```php
use Illuminate\Support\Facades\Hash;
use Illuminate\Validation\Rules\Password;

$validated = $request->validate([
    'password' => ['required', 'string', Password::min(12)->letters()->mixedCase()->numbers()->symbols()],
]);

$user->update(['password' => Hash::make($validated['password'])]);
```

## Authorization: Policies and Gates

## Authorization: Policies and Gates
## 授权：策略和门卫

- Use policies for model-level authorization
  - 使用策略进行模型级授权
- Enforce authorization in controllers and services
  - 在控制器和服务中强制执行授权

```php
$this->authorize('update', $project);
```

Use policy middleware for route-level enforcement:
使用策略中间件进行路由级强制执行：

```php
use Illuminate\Support\Facades\Route;

Route::put('/projects/{project}', [ProjectController::class, 'update'])
    ->middleware(['auth:sanctum', 'can:update,project']);
```

## Validation and Data Sanitization

## Validation and Data Sanitization
## 验证和数据清理

- Always validate inputs with Form Requests
  - 始终使用表单请求验证输入
- Use strict validation rules and type checks
  - 使用严格的验证规则和类型检查
- Never trust request payloads for derived fields
  - 绝不信任请求载荷中的派生字段

## Mass Assignment Protection

## Mass Assignment Protection
## 大量赋值保护

- Use `$fillable` or `$guarded` and avoid `Model::unguard()`
  - 使用 `$fillable` 或 `$guarded`，避免使用 `Model::unguard()`
- Prefer DTOs or explicit attribute mapping
  - 首选 DTO 或显式属性映射

## SQL Injection Prevention

## SQL Injection Prevention
## SQL 注入预防

- Use Eloquent or query builder parameter binding
  - 使用 Eloquent 或查询构建器的参数绑定
- Avoid raw SQL unless strictly necessary
  - 除非严格必要，否则避免原始 SQL

```php
DB::select('select * from users where email = ?', [$email]);
```

## XSS Prevention

## XSS Prevention
## XSS 预防

- Blade escapes output by default (`{{ }}`)
  - Blade 默认转义输出（`{{ }}`）
- Use `{!! !!}` only for trusted, sanitized HTML
  - 仅对受信任的、已清理的 HTML 使用 `{!! !!}`
- Sanitize rich text with a dedicated library
  - 使用专用库清理富文本

## CSRF Protection

## CSRF Protection
## CSRF 保护

- Keep `VerifyCsrfToken` middleware enabled
  - 保持 `VerifyCsrfToken` 中间件启用
- Include `@csrf` in forms and send XSRF tokens for SPA requests
  - 在表单中包含 `@csrf`，为 SPA 请求发送 XSRF 令牌

For SPA authentication with Sanctum, ensure stateful requests are configured:
对于使用 Sanctum 的 SPA 认证，确保配置了有状态请求：

```php
// config/sanctum.php
'stateful' => explode(',', env('SANCTUM_STATEFUL_DOMAINS', 'localhost')),
```

## File Upload Safety

## File Upload Safety
## 文件上传安全

- Validate file size, MIME type, and extension
  - 验证文件大小、MIME 类型和扩展名
- Store uploads outside the public path when possible
  - 尽可能将上传文件存储在公共路径之外
- Scan files for malware if required
  - 如有需要，扫描文件中的恶意软件

```php
final class UploadInvoiceRequest extends FormRequest
{
    public function authorize(): bool
    {
        return (bool) $this->user()?->can('upload-invoice');
    }

    public function rules(): array
    {
        return [
            'invoice' => ['required', 'file', 'mimes:pdf', 'max:5120'],
        ];
    }
}
```

```php
$path = $request->file('invoice')->store(
    'invoices',
    config('filesystems.private_disk', 'local') // set this to a non-public disk
                                                 // 将此设置为非公共磁盘
);
```

## Rate Limiting

## Rate Limiting
## 速率限制

- Apply `throttle` middleware on auth and write endpoints
  - 在认证和写端点上应用 `throttle` 中间件
- Use stricter limits for login, password reset, and OTP
  - 对登录、密码重置和 OTP 使用更严格的限制

```php
use Illuminate\Cache\RateLimiting\Limit;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\RateLimiter;

RateLimiter::for('login', function (Request $request) {
    return [
        Limit::perMinute(5)->by($request->ip()),
        Limit::perMinute(5)->by(strtolower((string) $request->input('email'))),
    ];
});
```

## Secrets and Credentials

## Secrets and Credentials
## 密钥和凭证

- Never commit secrets to source control
  - 绝不将密钥提交到源代码控制
- Use environment variables and secret managers
  - 使用环境变量和密钥管理器
- Rotate keys after exposure and invalidate sessions
  - 泄露后轮换密钥并使会话失效

## Encrypted Attributes

## Encrypted Attributes
## 加密属性

Use encrypted casts for sensitive columns at rest.
对静态敏感列使用加密转换。

```php
protected $casts = [
    'api_token' => 'encrypted',
];
```

## Security Headers

## Security Headers
## 安全头

- Add CSP, HSTS, and frame protection where appropriate
  - 在适当的地方添加 CSP、HSTS 和帧保护
- Use trusted proxy configuration to enforce HTTPS redirects
  - 使用受信任的代理配置强制执行 HTTPS 重定向

Example middleware to set headers:
设置头的中间件示例：

```php
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;

final class SecurityHeaders
{
    public function handle(Request $request, \Closure $next): Response
    {
        $response = $next($request);

        $response->headers->add([
            'Content-Security-Policy' => "default-src 'self'",
            'Strict-Transport-Security' => 'max-age=31536000', // add includeSubDomains/preload only when all subdomains are HTTPS
                                                                // 仅当所有子域都是 HTTPS 时才添加 includeSubDomains/preload
            'X-Frame-Options' => 'DENY',
            'X-Content-Type-Options' => 'nosniff',
            'Referrer-Policy' => 'no-referrer',
        ]);

        return $response;
    }
}
```

## CORS and API Exposure

## CORS and API Exposure
## CORS 和 API 暴露

- Restrict origins in `config/cors.php`
  - 在 `config/cors.php` 中限制来源
- Avoid wildcard origins for authenticated routes
  - 避免为认证路由使用通配符来源

```php
// config/cors.php
return [
    'paths' => ['api/*', 'sanctum/csrf-cookie'],
    'allowed_methods' => ['GET', 'POST', 'PUT', 'PATCH', 'DELETE'],
    'allowed_origins' => ['https://app.example.com'],
    'allowed_headers' => [
        'Content-Type',
        'Authorization',
        'X-Requested-With',
        'X-XSRF-TOKEN',
        'X-CSRF-TOKEN',
    ],
    'supports_credentials' => true,
];
```

## Logging and PII

## Logging and PII
## 日志记录和 PII

- Never log passwords, tokens, or full card data
  - 绝不记录密码、令牌或完整卡片数据
- Redact sensitive fields in structured logs
  - 在结构化日志中编辑敏感字段

```php
use Illuminate\Support\Facades\Log;

Log::info('User updated profile', [
    'user_id' => $user->id,
    'email' => '[REDACTED]',
    'token' => '[REDACTED]',
]);
```

## Dependency Security

## Dependency Security
## 依赖安全

- Run `composer audit` regularly
  - 定期运行 `composer audit`
- Pin dependencies with care and update promptly on CVEs
  - 谨慎固定依赖，在 CVE 时及时更新

## Signed URLs

## Signed URLs
## 签名 URL

Use signed routes for temporary, tamper-proof links.
使用签名路由生成临时的、防篡改的链接。

```php
use Illuminate\Support\Facades\URL;

$url = URL::temporarySignedRoute(
    'downloads.invoice',
    now()->addMinutes(15),
    ['invoice' => $invoice->id]
);
```

```php
use Illuminate\Support\Facades\Route;

Route::get('/invoices/{invoice}/download', [InvoiceController::class, 'download'])
    ->name('downloads.invoice')
    ->middleware('signed');
```
