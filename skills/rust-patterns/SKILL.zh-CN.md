---
name: rust-patterns
description: Idiomatic Rust patterns, ownership, error handling, traits, concurrency, and best practices for building safe, performant applications.
description zh-CN: 惯用 Rust 模式、所有权、错误处理、trait、并发，以及用于构建安全、高性能应用程序的最佳实践。
origin: ECC
---

# Rust Development Patterns

## Rust 开发模式

Idiomatic Rust patterns and best practices for building safe, performant, and maintainable applications.

用于构建安全、高性能和可维护应用程序的惯用 Rust 模式和最佳实践。

## When to Use

## When to Use
## 何时使用

- Writing new Rust code
  - 编写新的 Rust 代码
- Reviewing Rust code
  - 审查 Rust 代码
- Refactoring existing Rust code
  - 重构现有 Rust 代码
- Designing crate structure and module layout
  - 设计 crate 结构和模块布局

## How It Works

## How It Works
## 工作原理

This skill enforces idiomatic Rust conventions across six key areas: ownership and borrowing to prevent data races at compile time, `Result`/`?` error propagation with `thiserror` for libraries and `anyhow` for applications, enums and exhaustive pattern matching to make illegal states unrepresentable, traits and generics for zero-cost abstraction, safe concurrency via `Arc<Mutex<T>>`, channels, and async/await, and minimal `pub` surfaces organized by domain.

此 skill 强制执行惯用 Rust 约定，涵盖六个关键领域：所有权和借用（在编译时防止数据竞争）、使用 `thiserror` 的库和使用 `anyhow` 的应用程序的 `Result`/`?` 错误传播、枚举和穷尽模式匹配（使非法状态无法表示）、trait 和泛型（零成本抽象）、通过 `Arc<Mutex<T>>`、通道和 async/await 实现安全并发，以及按领域组织的最小 `pub` 暴露面。

## Core Principles

## Core Principles
## 核心原则

### 1. Ownership and Borrowing

### 1. Ownership and Borrowing
### 1. 所有权和借用

Rust's ownership system prevents data races and memory bugs at compile time.

Rust 的所有权系统在编译时防止数据竞争和内存错误。

```rust
// Good: Pass references when you don't need ownership
// 正确：当你不需要所有权时传递引用
fn process(data: &[u8]) -> usize {
    data.len()
}

// Good: Take ownership only when you need to store or consume
// 正确：只有在需要存储或消费时才获取所有权
fn store(data: Vec<u8>) -> Record {
    Record { payload: data }
}

// Bad: Cloning unnecessarily to avoid borrow checker
// 错误：不必要地克隆以避免借用检查器
fn process_bad(data: &Vec<u8>) -> usize {
    let cloned = data.clone(); // Wasteful — just borrow
    cloned.len()
}
```


### Use `Cow` for Flexible Ownership

### Use `Cow` for Flexible Ownership
### 使用 `Cow` 实现灵活所有权

```rust
use std::borrow::Cow;

fn normalize(input: &str) -> Cow<'_, str> {
    if input.contains(' ') {
        Cow::Owned(input.replace(' ', "_"))
    } else {
        Cow::Borrowed(input) // Zero-cost when no mutation needed
    }
}
```

## Error Handling

## Error Handling
## 错误处理

### Use `Result` and `?` — Never `unwrap()` in Production

### Use `Result` and `?` — Never `unwrap()` in Production
### 使用 `Result` 和 `?` — 生产代码中永不 `unwrap()`

```rust
// Good: Propagate errors with context
// 正确：用上下文传播错误
use anyhow::{Context, Result};

fn load_config(path: &str) -> Result<Config> {
    let content = std::fs::read_to_string(path)
        .with_context(|| format!("failed to read config from {path}"))?;
    let config: Config = toml::from_str(&content)
        .with_context(|| format!("failed to parse config from {path}"))?;
    Ok(config)
}

// Bad: Panics on error
// 错误：错误时 panic
fn load_config_bad(path: &str) -> Config {
    let content = std::fs::read_to_string(path).unwrap(); // Panics!
    toml::from_str(&content).unwrap()
}
```

### Library Errors with `thiserror`, Application Errors with `anyhow`

### Library Errors with `thiserror`, Application Errors with `anyhow`
### 库错误用 `thiserror`，应用错误用 `anyhow`

```rust
// Library code: structured, typed errors
// 库代码：结构化、类型化错误
use thiserror::Error;

#[derive(Debug, Error)]
pub enum StorageError {
    #[error("record not found: {id}")]
    NotFound { id: String },
    #[error("connection failed")]
    Connection(#[from] std::io::Error),
    #[error("invalid data: {0}")]
    InvalidData(String),
}

// Application code: flexible error handling
// 应用代码：灵活的错误处理
use anyhow::{bail, Result};

fn run() -> Result<()> {
    let config = load_config("app.toml")?;
    if config.workers == 0 {
        bail!("worker count must be > 0");
    }
    Ok(())
}
```

### `Option` Combinators Over Nested Matching

### `Option` Combinators Over Nested Matching
### 使用 `Option` 组合器而非嵌套匹配

```rust
// Good: Combinator chain
// 正确：组合器链
fn find_user_email(users: &[User], id: u64) -> Option<String> {
    users.iter()
        .find(|u| u.id == id)
        .map(|u| u.email.clone())
}

// Bad: Deeply nested matching
// 错误：深度嵌套匹配
fn find_user_email_bad(users: &[User], id: u64) -> Option<String> {
    match users.iter().find(|u| u.id == id) {
        Some(user) => match &user.email {
            email => Some(email.clone()),
        },
        None => None,
    }
}
```

## Enums and Pattern Matching

## Enums and Pattern Matching
## 枚举和模式匹配

### Model States as Enums

### Model States as Enums
### 用枚举建模状态

```rust
// Good: Impossible states are unrepresentable
// 正确：不可能的状态无法表示
enum ConnectionState {
    Disconnected,
    Connecting { attempt: u32 },
    Connected { session_id: String },
    Failed { reason: String, retries: u32 },
}

fn handle(state: &ConnectionState) {
    match state {
        ConnectionState::Disconnected => connect(),
        ConnectionState::Connecting { attempt } if *attempt > 3 => abort(),
        ConnectionState::Connecting { .. } => wait(),
        ConnectionState::Connected { session_id } => use_session(session_id),
        ConnectionState::Failed { retries, .. } if *retries < 5 => retry(),
        ConnectionState::Failed { reason, .. } => log_failure(reason),
    }
}
```

### Exhaustive Matching — No Catch-All for Business Logic

### Exhaustive Matching — No Catch-All for Business Logic
### 穷尽匹配 — 业务逻辑不用全匹配

```rust
// Good: Handle every variant explicitly
// 正确：显式处理每个变体
match command {
    Command::Start => start_service(),
    Command::Stop => stop_service(),
    Command::Restart => restart_service(),
    // Adding a new variant forces handling here
}

// Bad: Wildcard hides new variants
// 错误：通配符隐藏新变体
match command {
    Command::Start => start_service(),
    _ => {} // Silently ignores Stop, Restart, and future variants
}
```

## Traits and Generics

## Traits and Generics
## Trait 和泛型

### Accept Generics, Return Concrete Types

### Accept Generics, Return Concrete Types
### 接受泛型，返回具体类型

```rust
// Good: Generic input, concrete output
// 正确：泛型输入，具体输出
fn read_all(reader: &mut impl Read) -> std::io::Result<Vec<u8>> {
    let mut buf = Vec::new();
    reader.read_to_end(&mut buf)?;
    Ok(buf)
}

// Good: Trait bounds for multiple constraints
// 正确：多重约束的 trait bounds
fn process<T: Display + Send + 'static>(item: T) -> String {
    format!("processed: {item}")
}
```

### Trait Objects for Dynamic Dispatch

### Trait Objects for Dynamic Dispatch
### Trait 对象用于动态分发

```rust
// Use when you need heterogeneous collections or plugin systems
// 当需要异构集合或插件系统时使用
trait Handler: Send + Sync {
    fn handle(&self, request: &Request) -> Response;
}

struct Router {
    handlers: Vec<Box<dyn Handler>>,
}

// Use generics when you need performance (monomorphization)
// 当需要性能时使用泛型（单态化）
fn fast_process<H: Handler>(handler: &H, request: &Request) -> Response {
    handler.handle(request)
}
```

### Newtype Pattern for Type Safety

### Newtype Pattern for Type Safety
### 新类型模式保证类型安全

```rust
// Good: Distinct types prevent mixing up arguments
// 正确：不同类型防止参数混淆
struct UserId(u64);
struct OrderId(u64);

fn get_order(user: UserId, order: OrderId) -> Result<Order> {
    // Can't accidentally swap user and order IDs
    todo!()
}

// Bad: Easy to swap arguments
// 错误：容易交换参数
fn get_order_bad(user_id: u64, order_id: u64) -> Result<Order> {
    todo!()
}
```

## Structs and Data Modeling

## Structs and Data Modeling
## 结构体和数据建模

### Builder Pattern for Complex Construction

### Builder Pattern for Complex Construction
### 复杂构建用 Builder 模式

```rust
struct ServerConfig {
    host: String,
    port: u16,
    max_connections: usize,
}

impl ServerConfig {
    fn builder(host: impl Into<String>, port: u16) -> ServerConfigBuilder {
        ServerConfigBuilder { host: host.into(), port, max_connections: 100 }
    }
}

struct ServerConfigBuilder { host: String, port: u16, max_connections: usize }

impl ServerConfigBuilder {
    fn max_connections(mut self, n: usize) -> Self { self.max_connections = n; self }
    fn build(self) -> ServerConfig {
        ServerConfig { host: self.host, port: self.port, max_connections: self.max_connections }
    }
}

// Usage: ServerConfig::builder("localhost", 8080).max_connections(200).build()
```

## Iterators and Closures

## Iterators and Closures
## 迭代器和闭包

### Prefer Iterator Chains Over Manual Loops

### Prefer Iterator Chains Over Manual Loops
### 优先使用迭代器链而非手动循环

```rust
// Good: Declarative, lazy, composable
// 正确：声明式、惰性、可组合
let active_emails: Vec<String> = users.iter()
    .filter(|u| u.is_active)
    .map(|u| u.email.clone())
    .collect();

// Bad: Imperative accumulation
// 错误：命令式累积
let mut active_emails = Vec::new();
for user in &users {
    if user.is_active {
        active_emails.push(user.email.clone());
    }
}
```

### Use `collect()` with Type Annotation

### Use `collect()` with Type Annotation
### 使用类型注解的 `collect()`

```rust
// Collect into different types
// 收集为不同类型
let names: Vec<_> = items.iter().map(|i| &i.name).collect();
let lookup: HashMap<_, _> = items.iter().map(|i| (i.id, i)).collect();
let combined: String = parts.iter().copied().collect();

// Collect Results — short-circuits on first error
// 收集 Results — 在第一个错误时短路
let parsed: Result<Vec<i32>, _> = strings.iter().map(|s| s.parse()).collect();
```

## Concurrency

## Concurrency
## 并发

### `Arc<Mutex<T>>` for Shared Mutable State

### `Arc<Mutex<T>>` for Shared Mutable State
### 使用 `Arc<Mutex<T>>` 共享可变状态

```rust
use std::sync::{Arc, Mutex};

let counter = Arc::new(Mutex::new(0));
let handles: Vec<_> = (0..10).map(|_| {
    let counter = Arc::clone(&counter);
    std::thread::spawn(move || {
        let mut num = counter.lock().expect("mutex poisoned");
        *num += 1;
    })
}).collect();

for handle in handles {
    handle.join().expect("worker thread panicked");
}
```

### Channels for Message Passing

### Channels for Message Passing
### 使用通道传递消息

```rust
use std::sync::mpsc;

let (tx, rx) = mpsc::sync_channel(16); // Bounded channel with backpressure

for i in 0..5 {
    let tx = tx.clone();
    std::thread::spawn(move || {
        tx.send(format!("message {i}")).expect("receiver disconnected");
    });
}
drop(tx); // Close sender so rx iterator terminates

for msg in rx {
    println!("{msg}");
}
```

### Async with Tokio

### Async with Tokio
### 使用 Tokio 异步

```rust
use tokio::time::Duration;

async fn fetch_with_timeout(url: &str) -> Result<String> {
    let response = tokio::time::timeout(
        Duration::from_secs(5),
        reqwest::get(url),
    )
    .await
    .context("request timed out")?
    .context("request failed")?;

    response.text().await.context("failed to read body")
}

// Spawn concurrent tasks
// 生成并发任务
async fn fetch_all(urls: Vec<String>) -> Vec<Result<String>> {
    let handles: Vec<_> = urls.into_iter()
        .map(|url| tokio::spawn(async move {
            fetch_with_timeout(&url).await
        }))
        .collect();

    let mut results = Vec::with_capacity(handles.len());
    for handle in handles {
        results.push(handle.await.unwrap_or_else(|e| panic!("spawned task panicked: {e}")));
    }
    results
}
```

## Unsafe Code

## Unsafe Code
## 不安全代码

### When Unsafe Is Acceptable

### When Unsafe Is Acceptable
### 何时可接受 Unsafe

```rust
// Acceptable: FFI boundary with documented invariants (Rust 2024+)
// 可接受：带文档不变量的 FFI 边界
/// # Safety
/// `ptr` must be a valid, aligned pointer to an initialized `Widget`.
unsafe fn widget_from_raw<'a>(ptr: *const Widget) -> &'a Widget {
    // SAFETY: caller guarantees ptr is valid and aligned
    unsafe { &*ptr }
}

// Acceptable: Performance-critical path with proof of correctness
// 可接受：带正确性证明的性能关键路径
// SAFETY: index is always < len due to the loop bound
unsafe { slice.get_unchecked(index) }
```

### When Unsafe Is NOT Acceptable

### When Unsafe Is NOT Acceptable
### 何时不可接受 Unsafe

```rust
// Bad: Using unsafe to bypass borrow checker
// 错误：使用 unsafe 绕过借用检查器
// Bad: Using unsafe for convenience
// 错误：因便利使用 unsafe
// Bad: Using unsafe without a Safety comment
// 错误：没有 Safety 注释使用 unsafe
// Bad: Transmuting between unrelated types
// 错误：在不相关类型间转换
```

## Module System and Crate Structure

## Module System and Crate Structure
## 模块系统和 Crate 结构

### Organize by Domain, Not by Type

### Organize by Domain, Not by Type
### 按领域组织，而非按类型

```text
my_app/
├── src/
│   ├── main.rs
│   ├── lib.rs
│   ├── auth/          # Domain module
│   │   ├── mod.rs
│   │   ├── token.rs
│   │   └── middleware.rs
│   ├── orders/        # Domain module
│   │   ├── mod.rs
│   │   ├── model.rs
│   │   └── service.rs
│   └── db/            # Infrastructure
│       ├── mod.rs
│       └── pool.rs
├── tests/             # Integration tests
├── benches/           # Benchmarks
└── Cargo.toml
```

### Visibility — Expose Minimally

### Visibility — Expose Minimally
### 可见性 — 最小化暴露

```rust
// Good: pub(crate) for internal sharing
// 正确：内部共享用 pub(crate)
pub(crate) fn validate_input(input: &str) -> bool {
    !input.is_empty()
}

// Good: Re-export public API from lib.rs
// 正确：从 lib.rs 重新导出公共 API
pub mod auth;
pub use auth::AuthMiddleware;

// Bad: Making everything pub
// 错误：什么都设为 pub
pub fn internal_helper() {} // Should be pub(crate) or private
```

## Tooling Integration

## Tooling Integration
## 工具集成

### Essential Commands

### Essential Commands
### 常用命令

```bash
# Build and check
cargo build
cargo check              # Fast type checking without codegen
cargo clippy             # Lints and suggestions
cargo fmt                # Format code

# Testing
cargo test
cargo test -- --nocapture    # Show println output
cargo test --lib             # Unit tests only
cargo test --test integration # Integration tests only

# Dependencies
cargo audit              # Security audit
cargo tree               # Dependency tree
cargo update             # Update dependencies

# Performance
cargo bench              # Run benchmarks
```

## Quick Reference: Rust Idioms

## Quick Reference: Rust Idioms
## 快速参考：Rust 惯用法

| Idiom | Description | 惯用法 | 描述 |
|-------|-------------|--------|------|
| Borrow, don't clone | Pass `&T` instead of cloning unless ownership is needed | 借用，不要克隆 | 除非需要所有权，否则传递 `&T` 而非克隆 |
| Make illegal states unrepresentable | Use enums to model valid states only | 使非法状态无法表示 | 仅用枚举建模有效状态 |
| `?` over `unwrap()` | Propagate errors, never panic in library/production code | 用 `?` 而非 `unwrap()` | 传播错误，库/生产代码永不 panic |
| Parse, don't validate | Convert unstructured data to typed structs at the boundary | 解析，不要验证 | 在边界将非结构化数据转换为类型化结构体 |
| Newtype for type safety | Wrap primitives in newtypes to prevent argument swaps | 新类型保证类型安全 | 用新类型包装原始类型防止参数交换 |
| Prefer iterators over loops | Declarative chains are clearer and often faster | 优先使用迭代器而非循环 | 声明式链更清晰且通常更快 |
| `#[must_use]` on Results | Ensure callers handle return values | 在 Results 上用 `#[must_use]` | 确保调用者处理返回值 |
| `Cow` for flexible ownership | Avoid allocations when borrowing suffices | 用 `Cow` 实现灵活所有权 | 借用足够时避免分配 |
| Exhaustive matching | No wildcard `_` for business-critical enums | 穷尽匹配 | 业务关键枚举不用通配符 `_` |
| Minimal `pub` surface | Use `pub(crate)` for internal APIs | 最小化 `pub` 暴露面 | 内部 API 使用 `pub(crate)` |

## Anti-Patterns to Avoid

## Anti-Patterns to Avoid
## 应避免的反模式

```rust
// Bad: .unwrap() in production code
// 错误：生产代码中用 .unwrap()
let value = map.get("key").unwrap();

// Bad: .clone() to satisfy borrow checker without understanding why
// 错误：不理解原因地用 .clone() 满足借用检查器
let data = expensive_data.clone();
process(&original, &data);

// Bad: Using String when &str suffices
// 错误：当 &str 足够时使用 String
fn greet(name: String) { /* should be &str */ }

// Bad: Box<dyn Error> in libraries (use thiserror instead)
// 错误：库中用 Box<dyn Error>（用 thiserror）
fn parse(input: &str) -> Result<Data, Box<dyn std::error::Error>> { todo!() }

// Bad: Ignoring must_use warnings
// 错误：忽略 must_use 警告
let _ = validate(input); // Silently discarding a Result

// Bad: Blocking in async context
// 错误：在 async 上下文中阻塞
async fn bad_async() {
    std::thread::sleep(Duration::from_secs(1)); // Blocks the executor!
    // Use: tokio::time::sleep(Duration::from_secs(1)).await;
}
```

**Remember**: If it compiles, it's probably correct — but only if you avoid `unwrap()`, minimize `unsafe`, and let the type system work for you.

**记住**：能编译就可能正确——但前提是你避免 `unwrap()`、最小化 `unsafe`，并让类型系统为你工作。
