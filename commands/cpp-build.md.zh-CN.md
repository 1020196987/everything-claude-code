---
description: Fix C++ build errors, CMake issues, and linker problems incrementally. Invokes the cpp-build-resolver agent for minimal, surgical fixes.
description-zh: 增量修复 C++ 构建错误、CMake 问题和链接器问题。调用 cpp-build-resolver agent 进行最小的、精确的修复。
---

# C++ Build and Fix
# C++ 构建和修复

This command invokes the **cpp-build-resolver** agent to incrementally fix C++ build errors with minimal changes.
此命令调用 **cpp-build-resolver** agent 以最小的更改增量修复 C++ 构建错误。

## What This Command Does
## 此命令做什么

1. **Run Diagnostics**: Execute `cmake --build`, `clang-tidy`, `cppcheck`
1. **运行诊断**：执行 `cmake --build`、`clang-tidy`、`cppcheck`

2. **Parse Errors**: Group by file and sort by severity
2. **解析错误**：按文件分组，按严重性排序

3. **Fix Incrementally**: One error at a time
3. **增量修复**：一次一个错误

4. **Verify Each Fix**: Re-run build after each change
4. **验证每个修复**：每次更改后重新运行构建

5. **Report Summary**: Show what was fixed and what remains
5. **报告总结**：显示已修复和剩余内容

## When to Use
## 使用场景

Use `/cpp-build` when:
使用 `/cpp-build` 当：

- `cmake --build build` fails with errors
- `cmake --build build` 失败并报错

- Linker errors (undefined references, multiple definitions)
- 链接器错误（未定义引用、重复定义）

- Template instantiation failures
- 模板实例化失败

- Include/dependency issues
- Include/依赖问题

- After pulling changes that break the build
- 拉取更改后破坏构建

## Diagnostic Commands Run
## 运行的诊断命令

```bash
# CMake configure
# CMake 配置
cmake -B build -S .

# Build
# 构建
cmake --build build 2>&1 | head -100

# Static analysis (if available)
# 静态分析（如可用）
clang-tidy src/*.cpp -- -std=c++17
cppcheck --enable=all src/
```

## Example Session
## 示例会话

```text
User: /cpp-build

Agent:
# C++ Build Resolution

## Initial Diagnostics

```bash
$ cmake --build build
src/service/user.cpp:25:15: error: use of undeclared identifier 'UserRepository'
src/handler/api.cpp:42:9: error: no matching function for call to 'process'
src/handler/api.cpp:58:1: error: non-void function does not return a value
```

Errors found: 3

## Fix 1: Undeclared Identifier

File: src/service/user.cpp:25
Error: use of undeclared identifier 'UserRepository'
Cause: Missing include

```cpp
// Added include
#include "repository/user_repository.hpp"
```

```bash
$ cmake --build build
# 2 errors remaining
```

## Fix 2: No Matching Function

File: src/handler/api.cpp:42
Error: no matching function for call to 'process'

```cpp
// Changed
process(params.get("count"));
// To
process(std::stoi(params.get("count")));
```

```bash
$ cmake --build build
# 1 error remaining
```

## Fix 3: Missing Return

File: src/handler/api.cpp:58
Error: non-void function does not return a value

```cpp
std::optional<User> getUser(const std::string& id) {
    if (id.empty()) {
        return std::nullopt;
    }
    auto user = findUser(id);
    // Added missing return
    return user;
}
```

```bash
$ cmake --build build
# Build successful!
```

## Final Verification

```bash
$ ctest --test-dir build --output-on-failure
Test project build
    1/5 Test #1: unit_tests ........   Passed    0.02 sec
    2/5 Test #2: integration_tests    Passed    0.15 sec
All tests passed.
```

## Summary

| Metric | Count |
|--------|-------|
| Build errors fixed | 3 |
| Linker errors fixed | 0 |
| Files modified | 2 |
| Remaining issues | 0 |

Build Status: ✅ SUCCESS
```

## Common Errors Fixed
## 常见修复的错误

| Error | Typical Fix |
|-------|-------------|
| `undeclared identifier` | Add `#include` or fix typo |
| `未声明的标识符` | 添加 `#include` 或修复拼写错误 |
| `no matching function` | Fix argument types or add overload |
| `无匹配函数` | 修复参数类型或添加重载 |
| `undefined reference` | Link library or add implementation |
| `未定义引用` | 链接库或添加实现 |
| `multiple definition` | Use `inline` or move to .cpp |
| `重复定义` | 使用 `inline` 或移到 .cpp |
| `incomplete type` | Replace forward decl with `#include` |
| `不完整类型` | 用 `#include` 替换前向声明 |
| `no member named X` | Fix member name or include |
| `没有名为 X 的成员` | 修复成员名称或 include |
| `cannot convert X to Y` | Add appropriate cast |
| `无法将 X 转换为 Y` | 添加适当的类型转换 |
| `CMake Error` | Fix CMakeLists.txt configuration |
| `CMake 错误` | 修复 CMakeLists.txt 配置 |

## Fix Strategy
## 修复策略

1. **Compilation errors first** - Code must compile
1. **先编译错误** - 代码必须能编译

2. **Linker errors second** - Resolve undefined references
2. **其次链接器错误** - 解决未定义引用

3. **Warnings third** - Fix with `-Wall -Wextra`
3. **然后警告** - 使用 `-Wall -Wextra` 修复

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

- Missing external dependencies
- 缺少外部依赖

## Related Commands
## 相关命令

- `/cpp-test` - Run tests after build succeeds
- `/cpp-test` - 构建成功后运行测试

- `/cpp-review` - Review code quality
- `/cpp-review` - 审查代码质量

- `/verify` - Full verification loop
- `/verify` - 完整验证循环

## Related
## 相关

- Agent: `agents/cpp-build-resolver.md`
- Skill: `skills/cpp-coding-standards/`
