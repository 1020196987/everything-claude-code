---
name: patterns
description: Python design patterns and idioms
description: Python 设计模式和惯用法
---

# Patterns
# Python 模式

> This file extends [common/patterns.md](../common/patterns.md) with Python specific content.
> 此文件扩展了 [common/patterns.md](../common/patterns.md)，包含 Python 特定内容。

## Protocol (Duck Typing)
## 协议（鸭子类型）

```python
from typing import Protocol

class Repository(Protocol):
    def find_by_id(self, id: str) -> dict | None: ...
    def save(self, entity: dict) -> dict: ...
```

## Dataclasses as DTOs
## 数据类作为 DTO

```python
from dataclasses import dataclass

@dataclass
class CreateUserRequest:
    name: str
    email: str
    age: int | None = None
```

## Context Managers & Generators
## 上下文管理器和生成器

- Use context managers (`with` statement) for resource management
  使用上下文管理器（`with` 语句）进行资源管理
- Use generators for lazy evaluation and memory-efficient iteration
  使用生成器进行惰性求值和内存高效迭代

## Reference
## 参考

See skill: `python-patterns` for comprehensive patterns including decorators, concurrency, and package organization.
参见 skill: `python-patterns` 了解全面的模式，包括装饰器、并发和包组织。
