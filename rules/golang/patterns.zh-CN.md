---
name: patterns
description: Go design patterns and idioms
description: Go 设计模式和惯用法
---

# Patterns
# Go 模式

> This file extends [common/patterns.md](../common/patterns.md) with Go specific content.
> 此文件扩展了 [common/patterns.md](../common/patterns.md)，包含 Go 特定内容。

## Functional Options
## 函数式选项

```go
type Option func(*Server)

func WithPort(port int) Option {
    return func(s *Server) { s.port = port }
}

func NewServer(opts ...Option) *Server {
    s := &Server{port: 8080}
    for _, opt := range opts {
        opt(s)
    }
    return s
}
```

## Small Interfaces
## 小接口

Define interfaces where they are used, not where they are implemented.
在接口使用的地方定义接口，而非在接口实现的地方定义。

## Dependency Injection
## 依赖注入

Use constructor functions to inject dependencies:
使用构造函数注入依赖：

```go
func NewUserService(repo UserRepository, logger Logger) *UserService {
    return &UserService{repo: repo, logger: logger}
}
```

## Reference
## 参考

See skill: `golang-patterns` for comprehensive Go patterns including concurrency, error handling, and package organization.
参见 skill: `golang-patterns` 了解全面的 Go 模式，包括并发、错误处理和包组织。
