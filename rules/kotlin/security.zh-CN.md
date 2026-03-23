---
name: kotlin-security
description: Kotlin and Android security guidelines
description: Kotlin 和 Android 安全指南
paths:
  - "**/*.kt"
  - "**/*.kts"
---

# Kotlin Security
# Kotlin 安全

> This file extends [common/security.md](../common/security.md) with Kotlin and Android/KMP-specific content.
> 此文件扩展了 [common/security.md](../common/security.md)，包含 Kotlin 和 Android/KMP 特定内容。

## Secrets Management
## 密钥管理

- Never hardcode API keys, tokens, or credentials in source code
- 永不在源代码中硬编码 API 密钥、令牌或凭据
- Use `local.properties` (git-ignored) for local development secrets
- 对本地开发密钥使用 `local.properties`（git 忽略）
- Use `BuildConfig` fields generated from CI secrets for release builds
- 对发布构建使用从 CI 密钥生成的 `BuildConfig` 字段
- Use `EncryptedSharedPreferences` (Android) or Keychain (iOS) for runtime secret storage
- 对运行时密钥存储使用 `EncryptedSharedPreferences`（Android）或 Keychain（iOS）

```kotlin
// BAD
// 不好
val apiKey = "sk-abc123..."

// GOOD — from BuildConfig (generated at build time)
// 好 — 来自 BuildConfig（在构建时生成）
val apiKey = BuildConfig.API_KEY

// GOOD — from secure storage at runtime
// 好 — 来自运行时安全存储
val token = secureStorage.get("auth_token")
```

## Network Security
## 网络安全

- Use HTTPS exclusively — configure `network_security_config.xml` to block cleartext
- 仅使用 HTTPS — 配置 `network_security_config.xml` 阻止明文
- Pin certificates for sensitive endpoints using OkHttp `CertificatePinner` or Ktor equivalent
- 对敏感端点使用 OkHttp `CertificatePinner` 或 Ktor 等效项固定证书
- Set timeouts on all HTTP clients — never leave defaults (which may be infinite)
- 对所有 HTTP 客户端设置超时 — 永不保留默认值（可能是无限的）
- Validate and sanitize all server responses before use
- 使用前验证和清理所有服务器响应

```xml
<!-- res/xml/network_security_config.xml -->
<network-security-config>
    <base-config cleartextTrafficPermitted="false" />
</network-security-config>
```

## Input Validation
## 输入验证

- Validate all user input before processing or sending to API
- 在处理或发送到 API 之前验证所有用户输入
- Use parameterized queries for Room/SQLDelight — never concatenate user input into SQL
- 对 Room/SQLDelight 使用参数化查询 — 永不在 SQL 中连接用户输入
- Sanitize file paths from user input to prevent path traversal
- 清理用户输入中的文件路径以防止路径遍历

```kotlin
// BAD — SQL injection
// 不好 — SQL 注入
@Query("SELECT * FROM items WHERE name = '$input'")

// GOOD — parameterized
// 好 — 参数化
@Query("SELECT * FROM items WHERE name = :input")
fun findByName(input: String): List<ItemEntity>
```

## Data Protection
## 数据保护

- Use `EncryptedSharedPreferences` for sensitive key-value data on Android
- 对 Android 上的敏感键值数据使用 `EncryptedSharedPreferences`
- Use `@Serializable` with explicit field names — don't leak internal property names
- 使用带显式字段名的 `@Serializable` — 不要泄露内部属性名
- Clear sensitive data from memory when no longer needed
- 不再需要时从内存中清除敏感数据
- Use `@Keep` or ProGuard rules for serialized classes to prevent name mangling
- 对序列化类使用 `@Keep` 或 ProGuard 规则以防止名称混淆

## Authentication
## 身份验证

- Store tokens in secure storage, not in plain SharedPreferences
- 将令牌存储在安全存储中，而非明文 SharedPreferences
- Implement token refresh with proper 401/403 handling
- 使用正确的 401/403 处理实现令牌刷新
- Clear all auth state on logout (tokens, cached user data, cookies)
- 注销时清除所有认证状态（令牌、缓存的用户数据、cookie）
- Use biometric authentication (`BiometricPrompt`) for sensitive operations
- 对敏感操作使用生物识别认证（`BiometricPrompt`）

## ProGuard / R8

- Keep rules for all serialized models (`@Serializable`, Gson, Moshi)
- 对所有序列化模型保留规则（`@Serializable`、Gson、Moshi）
- Keep rules for reflection-based libraries (Koin, Retrofit)
- 对基于反射的库保留规则（Koin、Retrofit）
- Test release builds — obfuscation can break serialization silently
- 测试发布构建 — 混淆可能静默破坏序列化

## WebView Security
## WebView 安全

- Disable JavaScript unless explicitly needed: `settings.javaScriptEnabled = false`
- 除非明确需要，否则禁用 JavaScript：`settings.javaScriptEnabled = false`
- Validate URLs before loading in WebView
- 在 WebView 中加载前验证 URL
- Never expose `@JavascriptInterface` methods that access sensitive data
- 永不以暴露访问敏感数据的 `@JavascriptInterface` 方法
- Use `WebViewClient.shouldOverrideUrlLoading()` to control navigation
- 使用 `WebViewClient.shouldOverrideUrlLoading()` 控制导航
