---
name: django-verification
description: "Verification loop for Django projects: migrations, linting, tests with coverage, security scans, and deployment readiness checks before release or PR."
description zh-CN: "Django 项目验证循环：发布或 PR 前的迁移、linting、覆盖率测试、安全扫描和生产就绪检查。"
origin: ECC
---

# Django Verification Loop

## Django Verification Loop
## Django 验证循环

Run before PRs, after major changes, and pre-deploy to ensure Django application quality and security.
在 PR 之前、重大更改之后以及部署前运行，以确保 Django 应用程序的质量和安全性。

## When to Activate

## When to Activate
## 何时激活

- Before opening a pull request for a Django project
  为 Django 项目打开 PR 之前
- After major model changes, migration updates, or dependency upgrades
  重大模型更改、迁移更新或依赖升级后
- Pre-deployment verification for staging or production
  预演或生产部署前的验证
- Running full environment → lint → test → security → deploy readiness pipeline
  运行完整的环境 → lint → 测试 → 安全 → 部署就绪流水线
- Validating migration safety and test coverage
  验证迁移安全性和测试覆盖率

## Phase 1: Environment Check

## Phase 1: Environment Check
## 阶段 1：环境检查

```bash
# Verify Python version
# 验证 Python 版本
python --version  # Should match project requirements

# Check virtual environment
# 检查虚拟环境
which python
pip list --outdated

# Verify environment variables
# 验证环境变量
python -c "import os; import environ; print('DJANGO_SECRET_KEY set' if os.environ.get('DJANGO_SECRET_KEY') else 'MISSING: DJANGO_SECRET_KEY')"
```

If environment is misconfigured, stop and fix.
如果环境配置错误，停止并修复。

## Phase 2: Code Quality & Formatting

## Phase 2: Code Quality & Formatting
## 阶段 2：代码质量和格式

```bash
# Type checking
# 类型检查
mypy . --config-file pyproject.toml

# Linting with ruff
# 使用 ruff 进行 linting
ruff check . --fix

# Formatting with black
# 使用 black 格式化
black . --check
black .  # Auto-fix

# Import sorting
# 导入排序
isort . --check-only
isort .  # Auto-fix

# Django-specific checks
# Django 特定检查
python manage.py check --deploy
```

Common issues:
常见问题：

- Missing type hints on public functions
  公共函数缺少类型提示
- PEP 8 formatting violations
  PEP 8 格式违规
- Unsorted imports
  未排序的导入
- Debug settings left in production configuration
  生产配置中保留了调试设置

## Phase 3: Migrations

## Phase 3: Migrations
## 阶段 3：迁移

```bash
# Check for unapplied migrations
# 检查未应用的迁移
python manage.py showmigrations

# Create missing migrations
# 创建缺失的迁移
python manage.py makemigrations --check

# Dry-run migration application
# 迁移应用试运行
python manage.py migrate --plan

# Apply migrations (test environment)
# 应用迁移（测试环境）
python manage.py migrate

# Check for migration conflicts
# 检查迁移冲突
python manage.py makemigrations --merge  # Only if conflicts exist
```

Report:
报告：

- Number of pending migrations
  待处理迁移数量
- Any migration conflicts
  任何迁移冲突
- Model changes without migrations
  没有迁移的模型更改

## Phase 4: Tests + Coverage

## Phase 4: Tests + Coverage
## 阶段 4：测试 + 覆盖率

```bash
# Run all tests with pytest
# 使用 pytest 运行所有测试
pytest --cov=apps --cov-report=html --cov-report=term-missing --reuse-db

# Run specific app tests
# 运行特定应用测试
pytest apps/users/tests/

# Run with markers
# 使用标记运行
pytest -m "not slow"  # Skip slow tests
pytest -m integration  # Only integration tests

# Coverage report
# 覆盖率报告
open htmlcov/index.html
```

Report:
报告：

- Total tests: X passed, Y failed, Z skipped
  总测试数：X 通过，Y 失败，Z 跳过
- Overall coverage: XX%
  总体覆盖率：XX%
- Per-app coverage breakdown
  按应用划分覆盖率

Coverage targets:
覆盖率目标：

| Component | Target |
|-----------|--------|
| Models | 90%+ |
| Serializers | 85%+ |
| Views | 80%+ |
| Services | 90%+ |
| Overall | 80%+ |

| 组件 | 目标 |
|------|------|
| 模型 | 90%+ |
| 序列化器 | 85%+ |
| 视图 | 80%+ |
| 服务 | 90%+ |
| 总体 | 80%+ |

## Phase 5: Security Scan

## Phase 5: Security Scan
## 阶段 5：安全扫描

```bash
# Dependency vulnerabilities
# 依赖漏洞
pip-audit
safety check --full-report

# Django security checks
# Django 安全检查
python manage.py check --deploy

# Bandit security linter
# Bandit 安全 linter
bandit -r . -f json -o bandit-report.json

# Secret scanning (if gitleaks is installed)
# 密钥扫描（如果安装了 gitleaks）
gitleaks detect --source . --verbose

# Environment variable check
# 环境变量检查
python -c "from django.core.exceptions import ImproperlyConfigured; from django.conf import settings; settings.DEBUG"
```

Report:
报告：

- Vulnerable dependencies found
  发现的易受攻击的依赖
- Security configuration issues
  安全配置问题
- Hardcoded secrets detected
  检测到硬编码的密钥
- DEBUG mode status (should be False in production)
  DEBUG 模式状态（生产环境应为 False）

## Phase 6: Django Management Commands

## Phase 6: Django Management Commands
## 阶段 6：Django 管理命令

```bash
# Check for model issues
# 检查模型问题
python manage.py check

# Collect static files
# 收集静态文件
python manage.py collectstatic --noinput --clear

# Create superuser (if needed for tests)
# 创建超级用户（如果测试需要）
echo "from apps.users.models import User; User.objects.create_superuser('admin@example.com', 'admin')" | python manage.py shell

# Database integrity
# 数据库完整性
python manage.py check --database default

# Cache verification (if using Redis)
# 缓存验证（如果使用 Redis）
python -c "from django.core.cache import cache; cache.set('test', 'value', 10); print(cache.get('test'))"
```

## Phase 7: Performance Checks

## Phase 7: Performance Checks
## 阶段 7：性能检查

```bash
# Django Debug Toolbar output (check for N+1 queries)
# Django Debug Toolbar 输出（检查 N+1 查询）
# Run in dev mode with DEBUG=True and access a page
# 在 DEBUG=True 的开发模式下运行并访问页面
# Look for duplicate queries in SQL panel
# 在 SQL 面板中查找重复查询

# Query count analysis
# 查询计数分析
django-admin debugsqlshell  # If django-debug-sqlshell installed

# Check for missing indexes
# 检查缺失的索引
python manage.py shell << EOF
from django.db import connection
with connection.cursor() as cursor:
    cursor.execute("SELECT table_name, index_name FROM information_schema.statistics WHERE table_schema = 'public'")
    print(cursor.fetchall())
EOF
```

Report:
报告：

- Number of queries per page (should be < 50 for typical pages)
  每页查询数（典型页面应 < 50）
- Missing database indexes
  缺失的数据库索引
- Duplicate queries detected
  检测到的重复查询

## Phase 8: Static Assets

## Phase 8: Static Assets
## 阶段 8：静态资源

```bash
# Check for npm dependencies (if using npm)
# 检查 npm 依赖（如果使用 npm）
npm audit
npm audit fix

# Build static files (if using webpack/vite)
# 构建静态文件（如果使用 webpack/vite）
npm run build

# Verify static files
# 验证静态文件
ls -la staticfiles/
python manage.py findstatic css/style.css
```

## Phase 9: Configuration Review

## Phase 9: Configuration Review
## 阶段 9：配置审查

```python
# Run in Python shell to verify settings
# 在 Python shell 中运行以验证设置
python manage.py shell << EOF
from django.conf import settings
import os

# Critical checks
# 关键检查
checks = {
    'DEBUG is False': not settings.DEBUG,
    'SECRET_KEY set': bool(settings.SECRET_KEY and len(settings.SECRET_KEY) > 30),
    'ALLOWED_HOSTS set': len(settings.ALLOWED_HOSTS) > 0,
    'HTTPS enabled': getattr(settings, 'SECURE_SSL_REDIRECT', False),
    'HSTS enabled': getattr(settings, 'SECURE_HSTS_SECONDS', 0) > 0,
    'Database configured': settings.DATABASES['default']['ENGINE'] != 'django.db.backends.sqlite3',
}

for check, result in checks.items():
    status = '✓' if result else '✗'
    print(f"{status} {check}")
EOF
```

## Phase 10: Logging Configuration

## Phase 10: Logging Configuration
## 阶段 10：日志配置

```bash
# Test logging output
# 测试日志输出
python manage.py shell << EOF
import logging
logger = logging.getLogger('django')
logger.warning('Test warning message')
logger.error('Test error message')
EOF

# Check log files (if configured)
# 检查日志文件（如果已配置）
tail -f /var/log/django/django.log
```

## Phase 11: API Documentation (if DRF)

## Phase 11: API Documentation (if DRF)
## 阶段 11：API 文档（如果使用 DRF）

```bash
# Generate schema
# 生成 schema
python manage.py generateschema --format openapi-json > schema.json

# Validate schema
# 验证 schema
# Check if schema.json is valid JSON
# 检查 schema.json 是否为有效 JSON
python -c "import json; json.load(open('schema.json'))"

# Access Swagger UI (if using drf-yasg)
# 访问 Swagger UI（如果使用 drf-yasg）
# Visit http://localhost:8000/swagger/ in browser
# 在浏览器中访问 http://localhost:8000/swagger/
```

## Phase 12: Diff Review

## Phase 12: Diff Review
## 阶段 12：Diff 审查

```bash
# Show diff statistics
# 显示 diff 统计
git diff --stat

# Show actual changes
# 显示实际更改
git diff

# Show changed files
# 显示已更改的文件
git diff --name-only

# Check for common issues
# 检查常见问题
git diff | grep -i "todo\|fixme\|hack\|xxx"
git diff | grep "print("  # Debug statements
git diff | grep "DEBUG = True"  # Debug mode
git diff | grep "import pdb"  # Debugger
```

Checklist:
检查清单：

- No debugging statements (print, pdb, breakpoint())
  没有调试语句（print、pdb、breakpoint()）
- No TODO/FIXME comments in critical code
  关键代码中没有 TODO/FIXME 注释
- No hardcoded secrets or credentials
  没有硬编码的密钥或凭据
- Database migrations included for model changes
  模型更改包含数据库迁移
- Configuration changes documented
  配置更改已记录
- Error handling present for external calls
  外部调用存在错误处理
- Transaction management where needed
  需要的地方有事务管理

## Output Template

## Output Template
## 输出模板

```
DJANGO VERIFICATION REPORT
==========================

Phase 1: Environment Check
  ✓ Python 3.11.5
  ✓ Virtual environment active
  ✓ All environment variables set

Phase 2: Code Quality
  ✓ mypy: No type errors
  ✗ ruff: 3 issues found (auto-fixed)
  ✓ black: No formatting issues
  ✓ isort: Imports properly sorted
  ✓ manage.py check: No issues

Phase 3: Migrations
  ✓ No unapplied migrations
  ✓ No migration conflicts
  ✓ All models have migrations

Phase 4: Tests + Coverage
  Tests: 247 passed, 0 failed, 5 skipped
  Coverage:
    Overall: 87%
    users: 92%
    products: 89%
    orders: 85%
    payments: 91%

Phase 5: Security Scan
  ✗ pip-audit: 2 vulnerabilities found (fix required)
  ✓ safety check: No issues
  ✓ bandit: No security issues
  ✓ No secrets detected
  ✓ DEBUG = False

Phase 6: Django Commands
  ✓ collectstatic completed
  ✓ Database integrity OK
  ✓ Cache backend reachable

Phase 7: Performance
  ✓ No N+1 queries detected
  ✓ Database indexes configured
  ✓ Query count acceptable

Phase 8: Static Assets
  ✓ npm audit: No vulnerabilities
  ✓ Assets built successfully
  ✓ Static files collected

Phase 9: Configuration
  ✓ DEBUG = False
  ✓ SECRET_KEY configured
  ✓ ALLOWED_HOSTS set
  ✓ HTTPS enabled
  ✓ HSTS enabled
  ✓ Database configured

Phase 10: Logging
  ✓ Logging configured
  ✓ Log files writable

Phase 11: API Documentation
  ✓ Schema generated
  ✓ Swagger UI accessible

Phase 12: Diff Review
  Files changed: 12
  +450, -120 lines
  ✓ No debug statements
  ✓ No hardcoded secrets
  ✓ Migrations included

RECOMMENDATION: ⚠️ Fix pip-audit vulnerabilities before deploying

NEXT STEPS:
1. Update vulnerable dependencies
2. Re-run security scan
3. Deploy to staging for final testing
```

## Pre-Deployment Checklist

## Pre-Deployment Checklist
## 部署前检查清单

- [ ] All tests passing
  所有测试通过
- [ ] Coverage >= 80%
  覆盖率 >= 80%
- [ ] No security vulnerabilities
  无安全漏洞
- [ ] No unapplied migrations
  无未应用的迁移
- [ ] DEBUG = False in production settings
  生产设置中 DEBUG = False
- [ ] SECRET_KEY properly configured
  SECRET_KEY 正确配置
- [ ] ALLOWED_HOSTS set correctly
  ALLOWED_HOSTS 正确设置
- [ ] Database backups enabled
  数据库备份已启用
- [ ] Static files collected and served
  静态文件已收集并提供
- [ ] Logging configured and working
  日志已配置并正常工作
- [ ] Error monitoring (Sentry, etc.) configured
  错误监控（Sentry 等）已配置
- [ ] CDN configured (if applicable)
  CDN 已配置（如适用）
- [ ] Redis/cache backend configured
  Redis/缓存后端已配置
- [ ] Celery workers running (if applicable)
  Celery worker 正在运行（如适用）
- [ ] HTTPS/SSL configured
  HTTPS/SSL 已配置
- [ ] Environment variables documented
  环境变量已记录

## Continuous Integration

## Continuous Integration
## 持续集成

### GitHub Actions Example

### GitHub Actions Example
### GitHub Actions 示例

```yaml
# .github/workflows/django-verification.yml
name: Django Verification

on: [push, pull_request]

jobs:
  verify:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:14
        env:
          POSTGRES_PASSWORD: postgres
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5

    steps:
      - uses: actions/checkout@v3

      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'

      - name: Cache pip
        uses: actions/cache@v3
        with:
          path: ~/.cache/pip
          key: ${{ runner.os }}-pip-${{ hashFiles('**/requirements.txt') }}

      - name: Install dependencies
        run: |
          pip install -r requirements.txt
          pip install ruff black mypy pytest pytest-django pytest-cov bandit safety pip-audit

      - name: Code quality checks
        run: |
          ruff check .
          black . --check
          isort . --check-only
          mypy .

      - name: Security scan
        run: |
          bandit -r . -f json -o bandit-report.json
          safety check --full-report
          pip-audit

      - name: Run tests
        env:
          DATABASE_URL: postgres://postgres:postgres@localhost:5432/test
          DJANGO_SECRET_KEY: test-secret-key
        run: |
          pytest --cov=apps --cov-report=xml --cov-report=term-missing

      - name: Upload coverage
        uses: codecov/codecov-action@v3
```

## Quick Reference

## Quick Reference
## 快速参考

| Check | Command |
|-------|---------|
| Environment | `python --version` |
| Type checking | `mypy .` |
| Linting | `ruff check .` |
| Formatting | `black . --check` |
| Migrations | `python manage.py makemigrations --check` |
| Tests | `pytest --cov=apps` |
| Security | `pip-audit && bandit -r .` |
| Django check | `python manage.py check --deploy` |
| Collectstatic | `python manage.py collectstatic --noinput` |
| Diff stats | `git diff --stat` |

| 检查项 | 命令 |
|--------|------|
| 环境 | `python --version` |
| 类型检查 | `mypy .` |
| Linting | `ruff check .` |
| 格式化 | `black . --check` |
| 迁移 | `python manage.py makemigrations --check` |
| 测试 | `pytest --cov=apps` |
| 安全 | `pip-audit && bandit -r .` |
| Django 检查 | `python manage.py check --deploy` |
| 收集静态文件 | `python manage.py collectstatic --noinput` |
| Diff 统计 | `git diff --stat` |

Remember: Automated verification catches common issues but doesn't replace manual code review and testing in staging environment.

记住：自动化验证可以捕获常见问题，但不能取代在预演环境中的手动代码审查和测试。
