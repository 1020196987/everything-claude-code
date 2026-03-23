---
name: security
description: Go security guidelines
description: Go 安全指南
---

# Security
# Go 安全

> This file extends [common/security.md](../common/security.md) with Go specific content.
> 此文件扩展了 [common/security.md](../common/security.md)，包含 Go 特定内容。

## Secret Management
## 密钥管理

```go
apiKey := os.Getenv("OPENAI_API_KEY")
if apiKey == "" {
    log.Fatal("OPENAI_API_KEY not configured")
}
```

## Security Scanning
## 安全扫描

- Use **gosec** for static security analysis:
- 使用 **gosec** 进行静态安全分析：
  ```bash
  gosec ./...
  ```

## Context & Timeouts
## 上下文和超时

Always use `context.Context` for timeout control:
始终使用 `context.Context` 控制超时：

```go
ctx, cancel := context.WithTimeout(ctx, 5*time.Second)
defer cancel()
```
