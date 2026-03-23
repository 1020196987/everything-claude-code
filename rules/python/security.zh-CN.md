---
name: security
description: Python security guidelines
description: Python 安全指南
---

# Security
# Python 安全

> This file extends [common/security.md](../common/security.md) with Python specific content.
> 此文件扩展了 [common/security.md](../common/security.md)，包含 Python 特定内容。

## Secret Management
## 密钥管理

```python
import os
from dotenv import load_dotenv

load_dotenv()

api_key = os.environ["OPENAI_API_KEY"]  # Raises KeyError if missing / 如果缺失则抛出 KeyError
```

## Security Scanning
## 安全扫描

- Use **bandit** for static security analysis:
- 使用 **bandit** 进行静态安全分析：
  ```bash
  bandit -r src/
  ```

## Reference
## 参考

See skill: `django-security` for Django-specific security guidelines (if applicable).
参见 skill: `django-security` 了解 Django 特定的安全指南（如适用）。
