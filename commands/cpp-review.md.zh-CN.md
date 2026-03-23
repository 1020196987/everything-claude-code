---
description: Comprehensive C++ code review for memory safety, modern C++ idioms, concurrency, and security. Invokes the cpp-reviewer agent.
description-zh: 对 C++ 代码进行内存安全、现代 C++ 惯用法、并发和安全的综合代码审查。调用 cpp-reviewer agent。
---

# C++ Code Review
# C++ 代码审查

This command invokes the **cpp-reviewer** agent for comprehensive C++-specific code review.
此命令调用 **cpp-reviewer** agent 进行全面的 C++ 特定代码审查。

## What This Command Does
## 此命令做什么

1. **Identify C++ Changes**: Find modified `.cpp`, `.hpp`, `.cc`, `.h` files via `git diff`
1. **识别 C++ 更改**：通过 `git diff` 查找修改的 `.cpp`、`.hpp`、`.cc`、`.h` 文件

2. **Run Static Analysis**: Execute `clang-tidy` and `cppcheck`
2. **运行静态分析**：执行 `clang-tidy` 和 `cppcheck`

3. **Memory Safety Scan**: Check for raw new/delete, buffer overflows, use-after-free
3. **内存安全扫描**：检查原始 new/delete、缓冲区溢出、use-after-free

4. **Concurrency Review**: Analyze thread safety, mutex usage, data races
4. **并发审查**：分析线程安全、互斥锁使用、数据竞争

5. **Modern C++ Check**: Verify code follows C++17/20 conventions and best practices
5. **现代 C++ 检查**：验证代码遵循 C++17/20 约定和最佳实践

6. **Generate Report**: Categorize issues by severity
6. **生成报告**：按严重性分类问题

## When to Use
## 使用场景

Use `/cpp-review` when:
使用 `/cpp-review` 当：

- After writing or modifying C++ code
- 编写或修改 C++ 代码后

- Before committing C++ changes
- 提交 C++ 更改前

- Reviewing pull requests with C++ code
- 审查带有 C++ 代码的 PR

- Onboarding to a new C++ codebase
- 加入新的 C++ 代码库

- Checking for memory safety issues
- 检查内存安全问题

## Review Categories
## 审查类别

### CRITICAL (Must Fix)
### 严重（必须修复）

- Raw `new`/`delete` without RAII
- 无 RAII 的原始 `new`/`delete`

- Buffer overflows and use-after-free
- 缓冲区溢出和 use-after-free

- Data races without synchronization
- 无同步的数据竞争

- Command injection via `system()`
- 通过 `system()` 的命令注入

- Uninitialized variable reads
- 读取未初始化变量

- Null pointer dereferences
- 空指针解引用

### HIGH (Should Fix)
### 高（应该修复）

- Rule of Five violations
- 五法则违规

- Missing `std::lock_guard` / `std::scoped_lock`
- 缺少 `std::lock_guard` / `std::scoped_lock`

- Detached threads without proper lifetime management
- 无适当生命周期管理的分离线程

- C-style casts instead of `static_cast`/`dynamic_cast`
- 使用 C 风格转换而不是 `static_cast`/`dynamic_cast`

- Missing `const` correctness
- 缺少 `const` 正确性

### MEDIUM (Consider)
### 中（考虑修复）

- Unnecessary copies (pass by value instead of `const&`)
- 不必要的拷贝（传值而不是 `const&`）

- Missing `reserve()` on known-size containers
- 在已知大小的容器上缺少 `reserve()`

- `using namespace std;` in headers
- 在头文件中使用 `using namespace std;`

- Missing `[[nodiscard]]` on important return values
- 在重要返回值上缺少 `[[nodiscard]]`

- Overly complex template metaprogramming
- 过度复杂的模板元编程

## Automated Checks Run
## 自动运行检查

```bash
# Static analysis
# 静态分析
clang-tidy --checks='*,-llvmlibc-*' src/*.cpp -- -std=c++17

# Additional analysis
# 额外分析
cppcheck --enable=all --suppress=missingIncludeSystem src/

# Build with warnings
# 用警告构建
cmake --build build -- -Wall -Wextra -Wpedantic
```

## Example Usage
## 示例用法

```text
User: /cpp-review

Agent:
# C++ Code Review Report

## Files Reviewed
- src/handler/user.cpp (modified)
- src/service/auth.cpp (modified)

## Static Analysis Results
✓ clang-tidy: 2 warnings
✓ cppcheck: No issues

## Issues Found

[CRITICAL] Memory Leak
File: src/service/auth.cpp:45
Issue: Raw `new` without matching `delete`
```cpp
auto* session = new Session(userId);  // Memory leak!
cache[userId] = session;
```
Fix: Use `std::unique_ptr`
```cpp
auto session = std::make_unique<Session>(userId);
cache[userId] = std::move(session);
```

[HIGH] Missing const Reference
File: src/handler/user.cpp:28
Issue: Large object passed by value
```cpp
void processUser(User user) {  // Unnecessary copy
```
Fix: Pass by const reference
```cpp
void processUser(const User& user) {
```

## Summary
- CRITICAL: 1
- HIGH: 1
- MEDIUM: 0

Recommendation: ❌ Block merge until CRITICAL issue is fixed
```

## Approval Criteria
## 批准标准

| Status | Condition |
|--------|-----------|
| ✅ Approve | No CRITICAL or HIGH issues |
| ✅ 通过 | 无 CRITICAL 或 HIGH 问题 |
| ⚠️ Warning | Only MEDIUM issues (merge with caution) |
| ⚠️ 警告 | 仅 MEDIUM 问题（谨慎合并） |
| ❌ Block | CRITICAL or HIGH issues found |
| ❌ 阻止 | 发现 CRITICAL 或 HIGH 问题 |

## Integration with Other Commands
## 与其他命令集成

- Use `/cpp-test` first to ensure tests pass
- 先使用 `/cpp-test` 确保测试通过

- Use `/cpp-build` if build errors occur
- 如果发生构建错误，使用 `/cpp-build`

- Use `/cpp-review` before committing
- 提交前使用 `/cpp-review`

- Use `/code-review` for non-C++ specific concerns
- 使用 `/code-review` 处理非 C++ 特定问题

## Related
## 相关

- Agent: `agents/cpp-reviewer.md`
- Skills: `skills/cpp-coding-standards/`, `skills/cpp-testing/`
