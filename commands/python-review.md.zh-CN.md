---
description: Comprehensive Python code review for PEP 8 compliance, type hints, security, and Pythonic idioms. Invokes the python-reviewer agent.
description-zh: 对 Python 代码进行 PEP 8 合规性、类型提示、安全和 Python 惯用法的综合代码审查。调用 python-reviewer agent。
---

# Python Code Review
# Python 代码审查

This command invokes the **python-reviewer** agent for comprehensive Python-specific code review.
此命令调用 **python-reviewer** agent 进行全面的 Python 特定代码审查。

## What This Command Does
## 此命令做什么

1. **Identify Python Changes**: Find modified `.py` files via `git diff`
1. **识别 Python 更改**：通过 `git diff` 查找修改的 `.py` 文件

2. **Run Static Analysis**: Execute `ruff`, `mypy`, `pylint`, `black --check`
2. **运行静态分析**：执行 `ruff`、`mypy`、`pylint`、`black --check`

3. **Security Scan**: Check for SQL injection, command injection, unsafe deserialization
3. **安全扫描**：检查 SQL 注入、命令注入、不安全反序列化

4. **Type Safety Review**: Analyze type hints and mypy errors
4. **类型安全审查**：分析类型提示和 mypy 错误

5. **Pythonic Code Check**: Verify code follows PEP 8 and Python best practices
5. **Pythonic 代码检查**：验证代码遵循 PEP 8 和 Python 最佳实践

6. **Generate Report**: Categorize issues by severity
6. **生成报告**：按严重性分类问题

## When to Use
## 使用场景

Use `/python-review` when:
使用 `/python-review` 当：

- After writing or modifying Python code
- 编写或修改 Python 代码后

- Before committing Python changes
- 提交 Python 更改前

- Reviewing pull requests with Python code
- 审查带有 Python 代码的 PR

- Onboarding to a new Python codebase
- 加入新的 Python 代码库

- Learning Pythonic patterns and idioms
- 学习 Pythonic 模式和惯用法

## Review Categories
## 审查类别

### CRITICAL (Must Fix)
### 严重（必须修复）

- SQL/Command injection vulnerabilities
- SQL/命令注入漏洞

- Unsafe eval/exec usage
- 不安全的 eval/exec 使用

- Pickle unsafe deserialization
- Pickle 不安全反序列化

- Hardcoded credentials
- 硬编码凭证

- YAML unsafe load
- YAML 不安全加载

- Bare except clauses hiding errors
- 隐藏错误的裸露 except 子句

### HIGH (Should Fix)
### 高（应该修复）

- Missing type hints on public functions
- 公共函数缺少类型提示

- Mutable default arguments
- 可变默认参数

- Swallowing exceptions silently
- 静默吞掉异常

- Not using context managers for resources
- 不使用上下文管理器管理资源

- C-style looping instead of comprehensions
- 使用 C 风格循环而不是推导式

- Using type() instead of isinstance()
- 使用 type() 而不是 isinstance()

- Race conditions without locks
- 无锁的竞态条件

### MEDIUM (Consider)
### 中（考虑修复）

- PEP 8 formatting violations
- PEP 8 格式违规

- Missing docstrings on public functions
- 公共函数缺少文档字符串

- Print statements instead of logging
- 使用 print 语句而不是日志

- Inefficient string operations
- 低效的字符串操作

- Magic numbers without named constants
- 未命名常量的魔数

- Not using f-strings for formatting
- 不使用 f-string 进行格式化

- Unnecessary list creation
- 不必要的列表创建

## Automated Checks Run
## 自动运行检查

```bash
# Type checking
# 类型检查
mypy .

# Linting and formatting
# 格式检查和 linting
ruff check .
black --check .
isort --check-only .

# Security scanning
# 安全扫描
bandit -r .

# Dependency audit
# 依赖审计
pip-audit
safety check

# Testing
# 测试
pytest --cov=app --cov-report=term-missing
```

## Example Usage
## 示例用法

```text
User: /python-review

Agent:
# Python Code Review Report

## Files Reviewed
- app/routes/user.py (modified)
- app/services/auth.py (modified)

## Static Analysis Results
✓ ruff: No issues
✓ mypy: No errors
⚠️ black: 2 files need reformatting
✓ bandit: No security issues

## Issues Found

[CRITICAL] SQL Injection vulnerability
File: app/routes/user.py:42
Issue: User input directly interpolated into SQL query
```python
query = f"SELECT * FROM users WHERE id = {user_id}"  # Bad
```
Fix: Use parameterized query
```python
query = "SELECT * FROM users WHERE id = %s"  # Good
cursor.execute(query, (user_id,))
```

[HIGH] Mutable default argument
File: app/services/auth.py:18
Issue: Mutable default argument causes shared state
```python
def process_items(items=[]):  # Bad
    items.append("new")
    return items
```
Fix: Use None as default
```python
def process_items(items=None):  # Good
    if items is None:
        items = []
    items.append("new")
    return items
```

[MEDIUM] Missing type hints
File: app/services/auth.py:25
Issue: Public function without type annotations
```python
def get_user(user_id):  # Bad
    return db.find(user_id)
```
Fix: Add type hints
```python
def get_user(user_id: str) -> Optional[User]:  # Good
    return db.find(user_id)
```

[MEDIUM] Not using context manager
File: app/routes/user.py:55
Issue: File not closed on exception
```python
f = open("config.json")  # Bad
data = f.read()
f.close()
```
Fix: Use context manager
```python
with open("config.json") as f:  # Good
    data = f.read()
```

## Summary
- CRITICAL: 1
- HIGH: 1
- MEDIUM: 2

Recommendation: ❌ Block merge until CRITICAL issue is fixed

## Formatting Required
Run: `black app/routes/user.py app/services/auth.py`
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

- Use `/tdd` first to ensure tests pass
- 先使用 `/tdd` 确保测试通过

- Use `/code-review` for non-Python specific concerns
- 使用 `/code-review` 处理非 Python 特定问题

- Use `/python-review` before committing
- 提交前使用 `/python-review`

- Use `/build-fix` if static analysis tools fail
- 如果静态分析工具失败，使用 `/build-fix`

## Framework-Specific Reviews
## 框架特定审查

### Django Projects
### Django 项目

The reviewer checks for:
审查员检查：

- N+1 query issues (use `select_related` and `prefetch_related`)
- N+1 查询问题（使用 `select_related` 和 `prefetch_related`）

- Missing migrations for model changes
- 模型更改缺少迁移

- Raw SQL usage when ORM could work
- 在 ORM 可用时使用原始 SQL

- Missing `transaction.atomic()` for multi-step operations
- 多步骤操作缺少 `transaction.atomic()`

### FastAPI Projects
### FastAPI 项目

The reviewer checks for:
审查员检查：

- CORS misconfiguration
- CORS 错误配置

- Pydantic models for request validation
- 使用 Pydantic 模型进行请求验证

- Response models correctness
- 响应模型正确性

- Proper async/await usage
- 正确的 async/await 使用

- Dependency injection patterns
- 依赖注入模式

### Flask Projects
### Flask 项目

The reviewer checks for:
审查员检查：

- Context management (app context, request context)
- 上下文管理（app context、request context）

- Proper error handling
- 正确的错误处理

- Blueprint organization
- Blueprint 组织

- Configuration management
- 配置管理

## Related
## 相关

- Agent: `agents/python-reviewer.md`
- Skills: `skills/python-patterns/`, `skills/python-testing/`

## Common Fixes
## 常见修复

### Add Type Hints
### 添加类型提示

```python
# Before
# 之前
def calculate(x, y):
    return x + y

# After
# 之后
from typing import Union

def calculate(x: Union[int, float], y: Union[int, float]) -> Union[int, float]:
    return x + y
```

### Use Context Managers
### 使用上下文管理器

```python
# Before
# 之前
f = open("file.txt")
data = f.read()
f.close()

# After
# 之后
with open("file.txt") as f:
    data = f.read()
```

### Use List Comprehensions
### 使用列表推导式

```python
# Before
# 之前
result = []
for item in items:
    if item.active:
        result.append(item.name)

# After
# 之后
result = [item.name for item in items if item.active]
```

### Fix Mutable Defaults
### 修复可变默认值

```python
# Before
# 之前
def append(value, items=[]):
    items.append(value)
    return items

# After
# 之后
def append(value, items=None):
    if items is None:
        items = []
    items.append(value)
    return items
```

### Use f-strings (Python 3.6+)
### 使用 f-string（Python 3.6+）

```python
# Before
# 之前
name = "Alice"
greeting = "Hello, " + name + "!"
greeting2 = "Hello, {}".format(name)

# After
# 之后
greeting = f"Hello, {name}!"
```

### Fix String Concatenation in Loops
### 修复循环中的字符串拼接

```python
# Before
# 之前
result = ""
for item in items:
    result += str(item)

# After
# 之后
result = "".join(str(item) for item in items)
```

## Python Version Compatibility
## Python 版本兼容性

The reviewer notes when code uses features from newer Python versions:
审查员会标记代码是否使用了较新 Python 版本的功能：

| Feature | Minimum Python |
|---------|----------------|
| Type hints | 3.5+ |
| f-strings | 3.6+ |
| Walrus operator (`:=`) | 3.8+ |
| Position-only parameters | 3.8+ |
| Match statements | 3.10+ |
| Type unions (`x | None`) | 3.10+ |

Ensure your project's `pyproject.toml` or `setup.py` specifies the correct minimum Python version.
确保项目的 `pyproject.toml` 或 `setup.py` 指定了正确的最低 Python 版本。
