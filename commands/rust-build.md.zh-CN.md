---
description: Fix Rust build errors, borrow checker issues, and dependency problems incrementally. Invokes the rust-build-resolver agent for minimal, surgical fixes.
description-zh: 增量修复 Rust 构建错误、借用检查器问题和依赖问题。调用 rust-build-resolver agent 进行最小的、精确的修复。
---

# Rust Build and Fix
# Rust 构建和修复

This command invokes the **rust-build-resolver** agent to incrementally fix Rust build errors with minimal changes.
此命令调用 **rust-build-resolver** agent 以最小的更改增量修复 Rust 构建错误。

## What This Command Does
## 此命令做什么

1. **Run Diagnostics**: Execute `cargo check`, `cargo clippy`, `cargo fmt --check`
1. **运行诊断**：执行 `cargo check`、`cargo clippy`、`cargo fmt --check`

2. **Parse Errors**: Identify error codes and affected files
2. **解析错误**：识别错误代码和受影响的文件

3. **Fix Incrementally**: One error at a time
3. **增量修复**：一次一个错误

4. **Verify Each Fix**: Re-run `cargo check` after each change
4. **验证每个修复**：每次更改后重新运行 `cargo check`

5. **Report Summary**: Show what was fixed and what remains
5. **报告总结**：显示已修复和剩余内容

## When to Use
## 使用场景

Use `/rust-build` when:
使用 `/rust-build` 当：

- `cargo build` or `cargo check` fails with errors
- `cargo build` 或 `cargo check` 失败并报错

- `cargo clippy` reports warnings
- `cargo clippy` 报告警告

- Borrow checker or lifetime errors block compilation
- 借用检查器或生命周期错误阻止编译

- Cargo dependency resolution fails
- Cargo 依赖解析失败

- After pulling changes that break the build
- 拉取更改后破坏构建

## Diagnostic Commands Run
## 运行的诊断命令

```bash
# Primary build check
# 主要构建检查
cargo check 2>&1

# Lints and suggestions
# Lints 和建议
cargo clippy -- -D warnings 2>&1

# Formatting check
# 格式检查
cargo fmt --check 2>&1

# Dependency issues
# 依赖问题
cargo tree --duplicates

# Security audit (if available)
# 安全审计（如可用）
if command -v cargo-audit >/dev/null; then cargo audit; else echo "cargo-audit not installed"; fi
```

## Example Session
## 示例会话

````text
User: /rust-build

Agent:
# Rust Build Resolution

## Initial Diagnostics

```bash
$ cargo check
error[E0502]: cannot borrow `map` as mutable because it is also borrowed as immutable
  --> src/service/cache.rs:25:5
error[E0308]: mismatched types
  --> src/handler/api.rs:42:12
error[E0425]: cannot find value `db` in this scope
  --> src/handler/api.rs:58:9
```

Errors found: 3

## Fix 1: Borrow Checker Error

File: src/service/cache.rs:25
Error: E0502 — cannot borrow `map` as mutable because also borrowed as immutable
Cause: Immutable borrow still active during mutable insert

```rust
// Changed: restructured to end immutable borrow before mutable access
if !map.contains_key("key") {
    map.insert("key".into(), default);
}
```

```bash
$ cargo check
# 2 errors remaining
```

## Fix 2: Type Mismatch

File: src/handler/api.rs:42
Error: E0308 — mismatched types: expected `usize`, found `&str`

```rust
// Changed
let count = params.get("count");
// To
let count: usize = params.get("count")
    .and_then(|s| s.parse().ok())
    .unwrap_or(0);
```

```bash
$ cargo check
# 1 error remaining
```

## Fix 3: Unresolved Name

File: src/handler/api.rs:58
Error: E0425 — cannot find value `db` in this scope
Cause: Missing import

```rust
// Added
use crate::db;
```

```bash
$ cargo check
# Build successful!
```

## Final Verification

```bash
$ cargo clippy -- -D warnings
# No warnings

$ cargo test
running 6 tests
test service::cache::tests::test_insert ... ok
test service::cache::tests::test_get ... ok
test handler::api::tests::test_parse_count ... ok
test handler::api::tests::test_missing_count ... ok
test handler::api::tests::test_db_import ... ok
test handler::api::tests::test_response ... ok

test result: ok. 6 passed; 0 failed; 0 ignored
```

## Summary

| Metric | Count |
|--------|-------|
| Build errors fixed | 3 |
| Clippy warnings fixed | 0 |
| Files modified | 2 |
| Remaining issues | 0 |

Build Status: SUCCESS
````

## Common Errors Fixed
## 常见修复的错误

| Error | Typical Fix |
|-------|-------------|
| `cannot borrow as mutable` | Restructure to end immutable borrow first; clone only if justified |
| `无法可变借用` | 先重构以结束不可变借用；仅在必要时克隆 |
| `does not live long enough` | Use owned type or add lifetime annotation |
| `生命周期不够长` | 使用自有类型或添加生命周期注解 |
| `cannot move out of` | Restructure to take ownership; clone only as last resort |
| `无法移出` | 重构以获取所有权；不得已才克隆 |
| `mismatched types` | Add `.into()`, `as`, or explicit conversion |
| `类型不匹配` | 添加 `.into()`、`as` 或显式转换 |
| `trait X not implemented` | Add `#[derive(Trait)]` or implement manually |
| `未实现 trait X` | 添加 `#[derive(Trait)]` 或手动实现 |
| `unresolved import` | Add to Cargo.toml or fix `use` path |
| `无法解析的 import` | 添加到 Cargo.toml 或修复 `use` 路径 |
| `cannot find value` | Add import or fix path |
| `找不到值` | 添加 import 或修复路径 |

## Fix Strategy
## 修复策略

1. **Build errors first** - Code must compile
1. **先构建错误** - 代码必须能编译

2. **Clippy warnings second** - Fix suspicious constructs
2. **其次 Clippy 警告** - 修复可疑构造

3. **Formatting third** - `cargo fmt` compliance
3. **然后格式** - `cargo fmt` 合规

4. **One fix at a time** - Verify each change
4. **一次一个修复** - 验证每次更改

5. **Minimal changes** - Don't refactor, just fix
5. **最小更改** - 不重构，只修复

## Stop Conditions
## 停止条件

The agent will stop and report if:
如果以下情况，agent 将停止并报告：

- Same error persists after 3 attempts
- 同一错误在 3 次尝试后仍然存在

- Fix introduces more errors
- 修复引入了更多错误

- Requires architectural changes
- 需要架构更改

- Borrow checker error requires redesigning data ownership
- 借用检查器错误需要重新设计数据所有权

## Related Commands
## 相关命令

- `/rust-test` - Run tests after build succeeds
- `/rust-test` - 构建成功后运行测试

- `/rust-review` - Review code quality
- `/rust-review` - 审查代码质量

- `/verify` - Full verification loop
- `/verify` - 完整验证循环

## Related
## 相关

- Agent: `agents/rust-build-resolver.md`
- Skill: `skills/rust-patterns/`
