---
name: testing
description: Go testing frameworks and coverage
description: Go 测试框架和覆盖率
---

# Testing
# Go 测试

> This file extends [common/testing.md](../common/testing.md) with Go specific content.
> 此文件扩展了 [common/testing.md](../common/testing.md)，包含 Go 特定内容。

## Framework
## 框架

Use the standard `go test` with **table-driven tests**.
使用标准的 `go test` 配合**表驱动测试**。

## Race Detection
## 竞态检测

Always run with the `-race` flag:
始终使用 `-race` 标志运行：

```bash
go test -race ./...
```

## Coverage
## 覆盖率

```bash
go test -cover ./...
```

## Reference
## 参考

See skill: `golang-testing` for detailed Go testing patterns and helpers.
参见 skill: `golang-testing` 了解详细的 Go 测试模式和辅助函数。
