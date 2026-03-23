---
description: Comprehensive Rust code review for ownership, lifetimes, error handling, unsafe usage, and idiomatic patterns. Invokes the rust-reviewer agent.
description-zh: 对 Rust 代码进行所有权、生命周期、错误处理、不安全使用和惯用模式的综合代码审查。调用 rust-reviewer agent。
---

# Rust Code Review
# Rust 代码审查

This command invokes the **rust-reviewer** agent for comprehensive Rust-specific code review.
此命令调用 **rust-reviewer** agent 进行全面的 Rust 特定代码审查。

## What This Command Does
## 此命令做什么

1. **Verify Automated Checks**: Run `cargo check`, `cargo clippy -- -D warnings`, `cargo fmt --check`, and `cargo test` — stop if any fail
1. **验证自动检查**：运行 `cargo check`、`cargo clippy -- -D warnings`、`cargo fmt --check` 和 `cargo test` — 如果任何失败则停止

2. **Identify Rust Changes**: Find modified `.rs` files via `git diff HEAD~1` (or `git diff main...HEAD` for PRs)
2. **识别 Rust 更改**：通过 `git diff HEAD~1`（或 PR 的 `git diff main...HEAD`）查找修改的 `.rs` 文件

3. **Run Security Audit**: Execute `cargo audit` if available
3. **运行安全审计**：如果可用，执行 `cargo audit`

4. **Security Scan**: Check for unsafe usage, command injection, hardcoded secrets
4. **安全扫描**：检查不安全使用、命令注入、硬编码秘密

5. **Ownership Review**: Analyze unnecessary clones, lifetime issues, borrowing patterns
5. **所有权审查**：分析不必要的克隆、生命周期问题、借用模式

6. **Generate Report**: Categorize issues by severity
6. **生成报告**：按严重性分类问题

## When to Use
## 使用场景

Use `/rust-review` when:
使用 `/rust-review` 当：

- After writing or modifying Rust code
- 编写或修改 Rust 代码后

- Before committing Rust changes
- 提交 Rust 更改前

- Reviewing pull requests with Rust code
- 审查带有 Rust 代码的 PR

- Onboarding to a new Rust codebase
- 加入新的 Rust 代码库

- Learning idiomatic Rust patterns
- 学习惯用 Rust 模式

## Review Categories
## 审查类别

### CRITICAL (Must Fix)
### 严重（必须修复）

- Unchecked `unwrap()`/`expect()` in production code paths
- 生产代码路径中未检查的 `unwrap()`/`expect()`

- `unsafe` without `// SAFETY:` comment documenting invariants
- 无 `// SAFETY:` 注释记录不变量的 `unsafe`

- SQL injection via string interpolation in queries
- 通过字符串插值的 SQL 注入

- Command injection via unvalidated input in `std::process::Command`
- 通过 `std::process::Command` 中未验证输入的命令注入

- Hardcoded credentials
- 硬编码凭证

- Use-after-free via raw pointers
- 通过原始指针的 use-after-free

### HIGH (Should Fix)
### 高（应该修复）

- Unnecessary `.clone()` to satisfy borrow checker
- 为满足借用检查器的不必要 `.clone()`

- `String` parameter where `&str` or `impl AsRef<str>` suffices
- `String` 参数在 `&str` 或 `impl AsRef<str>` 足够的情况下

- Blocking in async context (`std::thread::sleep`, `std::fs`)
- 在异步上下文中的阻塞（`std::thread::sleep`、`std::fs`）

- Missing `Send`/`Sync` bounds on shared types
- 共享类型缺少 `Send`/`Sync` 约束

- Wildcard `_ =>` match on business-critical enums
- 在业务关键枚举上的通配符 `_ =>` 匹配

- Large functions (>50 lines)
- 大函数（>50 行）

### MEDIUM (Consider)
### 中（考虑修复）

- Unnecessary allocation in hot paths
- 热路径中的不必要分配

- Missing `with_capacity` when size is known
- 已知大小时缺少 `with_capacity`

- Suppressed clippy warnings without justification
- 无理由抑制 clippy 警告

- Public API without `///` documentation
- 公共 API 没有 `///` 文档

- Consider `#[must_use]` on non-`must_use` return types where ignoring values is likely a bug
- 在忽略值可能是 bug 的非 `must_use` 返回类型上考虑 `#[must_use]`

## Automated Checks Run
## 自动运行检查

```bash
# Build gate (must pass before review)
# 构建门槛（审查前必须通过）
cargo check

# Lints and suggestions
# Lints 和建议
cargo clippy -- -D warnings

# Formatting
# 格式化
cargo fmt --check

# Tests
# 测试
cargo test

# Security audit (if available)
# 安全审计（如可用）
if command -v cargo-audit >/dev/null; then cargo audit; else echo "cargo-audit not installed"; fi
```

## Example Usage
## 示例用法

````text
User: /rust-review

Agent:
# Rust Code Review Report

## Files Reviewed
- src/service/user.rs (modified)
- src/handler/api.rs (modified)

## Static Analysis Results
- Build: Successful
- Clippy: No warnings
- Formatting: Passed
- Tests: All passing

## Issues Found

[CRITICAL] Unchecked unwrap in Production Path
File: src/service/user.rs:28
Issue: Using `.unwrap()` on database query result
```rust
let user = db.find_by_id(id).unwrap();  // Panics on missing user
```
Fix: Propagate error with context
```rust
let user = db.find_by_id(id)
    .context("failed to fetch user")?;
```

[HIGH] Unnecessary Clone
File: src/handler/api.rs:45
Issue: Cloning String to satisfy borrow checker
```rust
let name = user.name.clone();
process(&user, &name);
```
Fix: Restructure to avoid clone
```rust
let result = process_name(&user.name);
use_user(&user, result);
```

## Summary
- CRITICAL: 1
- HIGH: 1
- MEDIUM: 0

Recommendation: Block merge until CRITICAL issue is fixed
````

## Approval Criteria
## 批准标准

| Status | Condition |
|--------|-----------|
| Approve | No CRITICAL or HIGH issues |
| 通过 | 无 CRITICAL 或 HIGH 问题 |
| Warning | Only MEDIUM issues (merge with caution) |
| 警告 | 仅 MEDIUM 问题（谨慎合并） |
| Block | CRITICAL or HIGH issues found |
| 阻止 | 发现 CRITICAL 或 HIGH 问题 |

## Integration with Other Commands
## 与其他命令集成

- Use `/rust-test` first to ensure tests pass
- 先使用 `/rust-test` 确保测试通过

- Use `/rust-build` if build errors occur
- 如果发生构建错误，使用 `/rust-build`

- Use `/rust-review` before committing
- 提交前使用 `/rust-review`

- Use `/code-review` for non-Rust-specific concerns
- 使用 `/code-review` 处理非 Rust 特定问题

## Related
## 相关

- Agent: `agents/rust-reviewer.md`
- Skills: `skills/rust-patterns/`, `skills/rust-testing/`
