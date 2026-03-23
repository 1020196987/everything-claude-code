---
name: testing
description: Python testing frameworks and coverage
description: Python 测试框架和覆盖率
---

# Testing
# Python 测试

> This file extends [common/testing.md](../common/testing.md) with Python specific content.
> 此文件扩展了 [common/testing.md](../common/testing.md)，包含 Python 特定内容。

## Framework
## 框架

Use **pytest** as the testing framework.
使用 **pytest** 作为测试框架。

## Coverage
## 覆盖率

```bash
pytest --cov=src --cov-report=term-missing
```

## Test Organization
## 测试组织

Use `pytest.mark` for test categorization:
使用 `pytest.mark` 进行测试分类：

```python
import pytest

@pytest.mark.unit
def test_calculate_total():
    ...

@pytest.mark.integration
def test_database_connection():
    ...
```

## Reference
## 参考

See skill: `python-testing` for detailed pytest patterns and fixtures.
参见 skill: `python-testing` 了解详细的 pytest 模式和 fixtures。
