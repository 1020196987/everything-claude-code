---
name: coding-style
description: Python coding style guidelines
description: Python 编码风格指南
---

# Coding Style
# Python 编码风格

> This file extends [common/coding-style.md](../common/coding-style.md) with Python specific content.
> 此文件扩展了 [common/coding-style.md](../common/coding-style.md)，包含 Python 特定内容。

## Standards
## 标准

- 遵循 **PEP 8** 约定（Follow **PEP 8** conventions）
- 在所有函数签名上使用**类型注解**（Use **type annotations** on all function signatures）

## Immutability
## 不可变性

Prefer immutable data structures:
优先使用不可变数据结构：

```python
from dataclasses import dataclass

@dataclass(frozen=True)
class User:
    name: str
    email: str

from typing import NamedTuple

class Point(NamedTuple):
    x: float
    y: float
```

## Formatting
## 格式化

- **black** for code formatting
  **black** 用于代码格式化
- **isort** for import sorting
  **isort** 用于导入排序
- **ruff** for linting
  **ruff** 用于 linting

## Reference
## 参考

See skill: `python-patterns` for comprehensive Python idioms and patterns.
参见 skill: `python-patterns` 了解全面的 Python 惯用法和模式。
