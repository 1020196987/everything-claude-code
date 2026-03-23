---
name: swift-security
description: Swift security guidelines
description: Swift 安全指南
paths:
  - "**/*.swift"
  - "**/Package.swift"
---

# Swift Security
# Swift 安全

> This file extends [common/security.md](../common/security.md) with Swift specific content.
> 此文件扩展了 [common/security.md](../common/security.md)，包含 Swift 特定内容。

## Secret Management
## 密钥管理

- Use **Keychain Services** for sensitive data (tokens, passwords, keys) — never `UserDefaults`
- 对敏感数据（令牌、密码、密钥）使用 **Keychain Services** — 永不使用 `UserDefaults`
- Use environment variables or `.xcconfig` files for build-time secrets
- 对构建时密钥使用环境变量或 `.xcconfig` 文件
- Never hardcode secrets in source — decompilation tools extract them trivially
- 永不在源代码中硬编码密钥 — 反编译工具可以轻易提取它们

```swift
let apiKey = ProcessInfo.processInfo.environment["API_KEY"]
guard let apiKey, !apiKey.isEmpty else {
    fatalError("API_KEY not configured")
}
```

## Transport Security
## 传输安全

- App Transport Security (ATS) is enforced by default — do not disable it
- App Transport Security (ATS) 默认强制启用 — 不要禁用它
- Use certificate pinning for critical endpoints
- 对关键端点使用证书固定
- Validate all server certificates
- 验证所有服务器证书

## Input Validation
## 输入验证

- Sanitize all user input before display to prevent injection
- 在显示前清理所有用户输入以防止注入
- Use `URL(string:)` with validation rather than force-unwrapping
- 使用带验证的 `URL(string:)` 而非强制解包
- Validate data from external sources (APIs, deep links, pasteboard) before processing
- 在处理前验证来自外部来源的数据（API、深链接、剪贴板）
