---
name: python-testing
description: Python testing strategies using pytest, TDD methodology, fixtures, mocking, parametrization, and coverage requirements.
description zh-CN: 使用pytest、TDD方法论、fixtures、模拟、参数化和覆盖率要求的Python测试策略。
origin: ECC
---

# Python Testing Patterns

## English

Comprehensive testing strategies for Python applications using pytest, TDD methodology, and best practices.

## 中文

使用pytest、TDD方法论和最佳实践的Python应用程序综合测试策略。

## When to Activate

## English

- Writing new Python code (follow TDD: red, green, refactor)
- Designing test suites for Python projects
- Reviewing Python test coverage
- Setting up testing infrastructure

## 中文

- 编写新Python代码（遵循TDD：红、绿、重构）
- 为Python项目设计测试套件
- 审查Python测试覆盖率
- 设置测试基础设施

## Core Testing Philosophy

## English

### Test-Driven Development (TDD)

Always follow the TDD cycle:

1. **RED**: Write a failing test for the desired behavior
2. **GREEN**: Write minimal code to make the test pass
3. **REFACTOR**: Improve code while keeping tests green

## 中文

### 测试驱动开发 (TDD)

始终遵循TDD周期：

1. **红**：为期望的行为编写一个失败的测试
2. **绿**：编写最少的代码使测试通过
3. **重构**：在保持测试通过的同时改进代码

```python
# Step 1: Write failing test (RED)
# 步骤1：编写失败的测试（红）
def test_add_numbers():
    result = add(2, 3)
    assert result == 5

# Step 2: Write minimal implementation (GREEN)
# 步骤2：编写最少的实现（绿）
def add(a, b):
    return a + b

# Step 3: Refactor if needed (REFACTOR)
# 步骤3：如有需要重构（重构）
```

### Coverage Requirements

## English

- **Target**: 80%+ code coverage
  **目标**：80%以上代码覆盖率
- **Critical paths**: 100% coverage required
  **关键路径**：需要100%覆盖率
- Use `pytest --cov` to measure coverage
  使用`pytest --cov`来测量覆盖率

## 中文

```bash
pytest --cov=mypackage --cov-report=term-missing --cov-report=html
```

## pytest Fundamentals

## English

### Basic Test Structure

## 中文

### 基础测试结构

```python
import pytest

def test_addition():
    """Test basic addition."""
    # 测试基础加法
    assert 2 + 2 == 4

def test_string_uppercase():
    """Test string uppercasing."""
    # 测试字符串大写
    text = "hello"
    assert text.upper() == "HELLO"

def test_list_append():
    """Test list append."""
    # 测试列表追加
    items = [1, 2, 3]
    items.append(4)
    assert 4 in items
    assert len(items) == 4
```

### Assertions

## English

## 中文

### 断言

```python
# Equality
# 相等
assert result == expected

# Inequality
# 不相等
assert result != unexpected

# Truthiness
# 真值
assert result  # Truthy 真值
assert not result  # Falsy 假值
assert result is True  # Exactly True 严格为True
assert result is False  # Exactly False 严格为False
assert result is None  # Exactly None 严格为None

# Membership
# 成员资格
assert item in collection
assert item not in collection

# Comparisons
# 比较
assert result > 0
assert 0 <= result <= 100

# Type checking
# 类型检查
assert isinstance(result, str)

# Exception testing (preferred approach)
# 异常测试（首选方法）
with pytest.raises(ValueError):
    raise ValueError("error message")

# Check exception message
# 检查异常消息
with pytest.raises(ValueError, match="invalid input"):
    raise ValueError("invalid input provided")

# Check exception attributes
# 检查异常属性
with pytest.raises(ValueError) as exc_info:
    raise ValueError("error message")
assert str(exc_info.value) == "error message"
```

## Fixtures

## English

### Basic Fixture Usage

## 中文

### 基础Fixture用法

```python
import pytest

@pytest.fixture
def sample_data():
    """Fixture providing sample data."""
    # 提供样本数据的fixture
    return {"name": "Alice", "age": 30}

def test_sample_data(sample_data):
    """Test using the fixture."""
    # 使用fixture进行测试
    assert sample_data["name"] == "Alice"
    assert sample_data["age"] == 30
```

### Fixture with Setup/Teardown

## English

## 中文

### 带设置/拆卸的Fixture

```python
@pytest.fixture
def database():
    """Fixture with setup and teardown."""
    # 带设置和拆卸的fixture
    # Setup
    # 设置
    db = Database(":memory:")
    db.create_tables()
    db.insert_test_data()

    yield db  # Provide to test 提供给测试

    # Teardown
    # 拆卸
    db.close()

def test_database_query(database):
    """Test database operations."""
    # 测试数据库操作
    result = database.query("SELECT * FROM users")
    assert len(result) > 0
```

### Fixture Scopes

## English

## 中文

### Fixture作用域

```python
# Function scope (default) - runs for each test
# 函数作用域（默认）- 每个测试运行一次
@pytest.fixture
def temp_file():
    with open("temp.txt", "w") as f:
        yield f
    os.remove("temp.txt")

# Module scope - runs once per module
# 模块作用域 - 每个模块运行一次
@pytest.fixture(scope="module")
def module_db():
    db = Database(":memory:")
    db.create_tables()
    yield db
    db.close()

# Session scope - runs once per test session
# 会话作用域 - 每个测试会话运行一次
@pytest.fixture(scope="session")
def shared_resource():
    resource = ExpensiveResource()
    yield resource
    resource.cleanup()
```

### Fixture with Parameters

## English

## 中文

### 带参数的Fixture

```python
@pytest.fixture(params=[1, 2, 3])
def number(request):
    """Parameterized fixture."""
    # 参数化fixture
    return request.param

def test_numbers(number):
    """Test runs 3 times, once for each parameter."""
    # 测试运行3次，每个参数一次
    assert number > 0
```

### Using Multiple Fixtures

## English

## 中文

### 使用多个Fixtures

```python
@pytest.fixture
def user():
    return User(id=1, name="Alice")

@pytest.fixture
def admin():
    return User(id=2, name="Admin", role="admin")

def test_user_admin_interaction(user, admin):
    """Test using multiple fixtures."""
    # 使用多个fixture进行测试
    assert admin.can_manage(user)
```

### Autouse Fixtures

## English

## 中文

### 自动使用Fixtures

```python
@pytest.fixture(autouse=True)
def reset_config():
    """Automatically runs before every test."""
    # 自动在每个测试前运行
    Config.reset()
    yield
    Config.cleanup()

def test_without_fixture_call():
    # reset_config runs automatically
    # reset_config自动运行
    assert Config.get_setting("debug") is False
```

### Conftest.py for Shared Fixtures

## English

## 中文

### 用于共享Fixtures的conftest.py

```python
# tests/conftest.py
import pytest

@pytest.fixture
def client():
    """Shared fixture for all tests."""
    # 所有测试的共享fixture
    app = create_app(testing=True)
    with app.test_client() as client:
        yield client

@pytest.fixture
def auth_headers(client):
    """Generate auth headers for API testing."""
    # 为API测试生成认证头
    response = client.post("/api/login", json={
        "username": "test",
        "password": "test"
    })
    token = response.json["token"]
    return {"Authorization": f"Bearer {token}"}
```

## Parametrization

## English

### Basic Parametrization

## 中文

### 基础参数化

```python
@pytest.mark.parametrize("input,expected", [
    ("hello", "HELLO"),
    ("world", "WORLD"),
    ("PyThOn", "PYTHON"),
])
def test_uppercase(input, expected):
    """Test runs 3 times with different inputs."""
    # 使用不同输入运行3次测试
    assert input.upper() == expected
```

### Multiple Parameters

## English

## 中文

### 多个参数

```python
@pytest.mark.parametrize("a,b,expected", [
    (2, 3, 5),
    (0, 0, 0),
    (-1, 1, 0),
    (100, 200, 300),
])
def test_add(a, b, expected):
    """Test addition with multiple inputs."""
    # 使用多个输入测试加法
    assert add(a, b) == expected
```

### Parametrize with IDs

## English

## 中文

### 带ID的参数化

```python
@pytest.mark.parametrize("input,expected", [
    ("valid@email.com", True),
    ("invalid", False),
    ("@no-domain.com", False),
], ids=["valid-email", "missing-at", "missing-domain"])
def test_email_validation(input, expected):
    """Test email validation with readable test IDs."""
    # 使用可读的测试ID测试邮箱验证
    assert is_valid_email(input) is expected
```

### Parametrized Fixtures

## English

## 中文

### 参数化Fixtures

```python
@pytest.fixture(params=["sqlite", "postgresql", "mysql"])
def db(request):
    """Test against multiple database backends."""
    # 针对多个数据库后端进行测试
    if request.param == "sqlite":
        return Database(":memory:")
    elif request.param == "postgresql":
        return Database("postgresql://localhost/test")
    elif request.param == "mysql":
        return Database("mysql://localhost/test")

def test_database_operations(db):
    """Test runs 3 times, once for each database."""
    # 测试运行3次，每个数据库一次
    result = db.query("SELECT 1")
    assert result is not None
```

## Markers and Test Selection

## English

### Custom Markers

## 中文

### 自定义标记

```python
# Mark slow tests
# 标记慢速测试
@pytest.mark.slow
def test_slow_operation():
    time.sleep(5)

# Mark integration tests
# 标记集成测试
@pytest.mark.integration
def test_api_integration():
    response = requests.get("https://api.example.com")
    assert response.status_code == 200

# Mark unit tests
# 标记单元测试
@pytest.mark.unit
def test_unit_logic():
    assert calculate(2, 3) == 5
```

### Run Specific Tests

## English

## 中文

### 运行特定测试

```bash
# Run only fast tests
# 只运行快速测试
pytest -m "not slow"

# Run only integration tests
# 只运行集成测试
pytest -m integration

# Run integration or slow tests
# 运行集成或慢速测试
pytest -m "integration or slow"

# Run tests marked as unit but not slow
# 运行标记为unit但不是slow的测试
pytest -m "unit and not slow"
```

### Configure Markers in pytest.ini

## English

## 中文

### 在pytest.ini中配置标记

```ini
[pytest]
markers =
    slow: marks tests as slow
    integration: marks tests as integration tests
    unit: marks tests as unit tests
    django: marks tests as requiring Django
```

## Mocking and Patching

## English

### Mocking Functions

## 中文

### 模拟函数

```python
from unittest.mock import patch, Mock

@patch("mypackage.external_api_call")
def test_with_mock(api_call_mock):
    """Test with mocked external API."""
    # 使用模拟的外部API进行测试
    api_call_mock.return_value = {"status": "success"}

    result = my_function()

    api_call_mock.assert_called_once()
    assert result["status"] == "success"
```

### Mocking Return Values

## English

## 中文

### 模拟返回值

```python
@patch("mypackage.Database.connect")
def test_database_connection(connect_mock):
    """Test with mocked database connection."""
    # 使用模拟的数据库连接进行测试
    connect_mock.return_value = MockConnection()

    db = Database()
    db.connect()

    connect_mock.assert_called_once_with("localhost")
```

### Mocking Exceptions

## English

## 中文

### 模拟异常

```python
@patch("mypackage.api_call")
def test_api_error_handling(api_call_mock):
    """Test error handling with mocked exception."""
    # 使用模拟的异常测试错误处理
    api_call_mock.side_effect = ConnectionError("Network error")

    with pytest.raises(ConnectionError):
        api_call()

    api_call_mock.assert_called_once()
```

### Mocking Context Managers

## English

## 中文

### 模拟上下文管理器

```python
@patch("builtins.open", new_callable=mock_open)
def test_file_reading(mock_file):
    """Test file reading with mocked open."""
    # 使用模拟的open测试文件读取
    mock_file.return_value.read.return_value = "file content"

    result = read_file("test.txt")

    mock_file.assert_called_once_with("test.txt", "r")
    assert result == "file content"
```

### Using Autospec

## English

## 中文

### 使用Autospec

```python
@patch("mypackage.DBConnection", autospec=True)
def test_autospec(db_mock):
    """Test with autospec to catch API misuse."""
    # 使用autospec捕获API误用
    db = db_mock.return_value
    db.query("SELECT * FROM users")

    # This would fail if DBConnection doesn't have query method
    # 如果DBConnection没有query方法，这会失败
    db_mock.assert_called_once()
```

### Mock Class Instances

## English

## 中文

### 模拟类实例

```python
class TestUserService:
    @patch("mypackage.UserRepository")
    def test_create_user(self, repo_mock):
        """Test user creation with mocked repository."""
        # 使用模拟的仓库测试用户创建
        repo_mock.return_value.save.return_value = User(id=1, name="Alice")

        service = UserService(repo_mock.return_value)
        user = service.create_user(name="Alice")

        assert user.name == "Alice"
        repo_mock.return_value.save.assert_called_once()
```

### Mock Property

## English

## 中文

### 模拟属性

```python
@pytest.fixture
def mock_config():
    """Create a mock with a property."""
    # 创建带属性的模拟
    config = Mock()
    type(config).debug = PropertyMock(return_value=True)
    type(config).api_key = PropertyMock(return_value="test-key")
    return config

def test_with_mock_config(mock_config):
    """Test with mocked config properties."""
    # 使用模拟的配置属性进行测试
    assert mock_config.debug is True
    assert mock_config.api_key == "test-key"
```

## Testing Async Code

## English

### Async Tests with pytest-asyncio

## 中文

### 使用pytest-asyncio进行异步测试

```python
import pytest

@pytest.mark.asyncio
async def test_async_function():
    """Test async function."""
    # 测试异步函数
    result = await async_add(2, 3)
    assert result == 5

@pytest.mark.asyncio
async def test_async_with_fixture(async_client):
    """Test async with async fixture."""
    # 使用异步fixture测试异步
    response = await async_client.get("/api/users")
    assert response.status_code == 200
```

### Async Fixture

## English

## 中文

### 异步Fixture

```python
@pytest.fixture
async def async_client():
    """Async fixture providing async test client."""
    # 提供异步测试客户端的异步fixture
    app = create_app()
    async with app.test_client() as client:
        yield client

@pytest.mark.asyncio
async def test_api_endpoint(async_client):
    """Test using async fixture."""
    # 使用异步fixture进行测试
    response = await async_client.get("/api/data")
    assert response.status_code == 200
```

### Mocking Async Functions

## English

## 中文

### 模拟异步函数

```python
@pytest.mark.asyncio
@patch("mypackage.async_api_call")
async def test_async_mock(api_call_mock):
    """Test async function with mock."""
    # 使用模拟测试异步函数
    api_call_mock.return_value = {"status": "ok"}

    result = await my_async_function()

    api_call_mock.assert_awaited_once()
    assert result["status"] == "ok"
```

## Testing Exceptions

## English

### Testing Expected Exceptions

## 中文

### 测试预期异常

```python
def test_divide_by_zero():
    """Test that dividing by zero raises ZeroDivisionError."""
    # 测试除以零引发ZeroDivisionError
    with pytest.raises(ZeroDivisionError):
        divide(10, 0)

def test_custom_exception():
    """Test custom exception with message."""
    # 测试带消息的自定义异常
    with pytest.raises(ValueError, match="invalid input"):
        validate_input("invalid")
```

### Testing Exception Attributes

## English

## 中文

### 测试异常属性

```python
def test_exception_with_details():
    """Test exception with custom attributes."""
    # 测试带自定义属性的异常
    with pytest.raises(CustomError) as exc_info:
        raise CustomError("error", code=400)

    assert exc_info.value.code == 400
    assert "error" in str(exc_info.value)
```

## Testing Side Effects

## English

### Testing File Operations

## 中文

### 测试文件操作

```python
import tempfile
import os

def test_file_processing():
    """Test file processing with temp file."""
    # 使用临时文件测试文件处理
    with tempfile.NamedTemporaryFile(mode='w', delete=False, suffix='.txt') as f:
        f.write("test content")
        temp_path = f.name

    try:
        result = process_file(temp_path)
        assert result == "processed: test content"
    finally:
        os.unlink(temp_path)
```

### Testing with pytest's tmp_path Fixture

## English

## 中文

### 使用pytest的tmp_path Fixture

```python
def test_with_tmp_path(tmp_path):
    """Test using pytest's built-in temp path fixture."""
    # 使用pytest内置的临时路径fixture
    test_file = tmp_path / "test.txt"
    test_file.write_text("hello world")

    result = process_file(str(test_file))
    assert result == "hello world"
    # tmp_path automatically cleaned up
    # tmp_path自动清理
```

### Testing with tmpdir Fixture

## English

## 中文

### 使用tmpdir Fixture

```python
def test_with_tmpdir(tmpdir):
    """Test using pytest's tmpdir fixture."""
    # 使用pytest的tmpdir fixture
    test_file = tmpdir.join("test.txt")
    test_file.write("data")

    result = process_file(str(test_file))
    assert result == "data"
```

## Test Organization

## English

### Directory Structure

## 中文

### 目录结构

```
tests/
├── conftest.py                 # Shared fixtures 共享fixtures
├── __init__.py
├── unit/                       # Unit tests 单元测试
│   ├── __init__.py
│   ├── test_models.py
│   ├── test_utils.py
│   └── test_services.py
├── integration/                # Integration tests 集成测试
│   ├── __init__.py
│   ├── test_api.py
│   └── test_database.py
└── e2e/                        # End-to-end tests 端到端测试
    ├── __init__.py
    └── test_user_flow.py
```

### Test Classes

## English

## 中文

### 测试类

```python
class TestUserService:
    """Group related tests in a class."""
    # 将相关测试分组到类中

    @pytest.fixture(autouse=True)
    def setup(self):
        """Setup runs before each test in this class."""
        # 设置在此类的每个测试前运行
        self.service = UserService()

    def test_create_user(self):
        """Test user creation."""
        # 测试用户创建
        user = self.service.create_user("Alice")
        assert user.name == "Alice"

    def test_delete_user(self):
        """Test user deletion."""
        # 测试用户删除
        user = User(id=1, name="Bob")
        self.service.delete_user(user)
        assert not self.service.user_exists(1)
```

## Best Practices

## English

### DO

## 中文

### 应该做

- **Follow TDD**: Write tests before code (red-green-refactor)
  **遵循TDD**：在编写代码之前写测试（红-绿-重构）
- **Test one thing**: Each test should verify a single behavior
  **测试一件事**：每个测试应该验证一个行为
- **Use descriptive names**: `test_user_login_with_invalid_credentials_fails`
  **使用描述性名称**：`test_user_login_with_invalid_credentials_fails`
- **Use fixtures**: Eliminate duplication with fixtures
  **使用fixtures**：用fixtures消除重复
- **Mock external dependencies**: Don't depend on external services
  **模拟外部依赖**：不依赖外部服务
- **Test edge cases**: Empty inputs, None values, boundary conditions
  **测试边界情况**：空输入、None值、边界条件
- **Aim for 80%+ coverage**: Focus on critical paths
  **目标是80%以上覆盖率**：关注关键路径
- **Keep tests fast**: Use marks to separate slow tests
  **保持测试快速**：用标记分离慢速测试

### DON'T

## English

## 中文

### 不应该做

- **Don't test implementation**: Test behavior, not internals
  **不要测试实现**：测试行为，不是内部细节
- **Don't use complex conditionals in tests**: Keep tests simple
  **不要在测试中使用复杂条件**：保持测试简单
- **Don't ignore test failures**: All tests must pass
  **不要忽略测试失败**：所有测试必须通过
- **Don't test third-party code**: Trust libraries to work
  **不要测试第三方代码**：信任库能正常工作
- **Don't share state between tests**: Tests should be independent
  **不要在测试之间共享状态**：测试应该是独立的
- **Don't catch exceptions in tests**: Use `pytest.raises`
  **不要在测试中捕获异常**：使用`pytest.raises`
- **Don't use print statements**: Use assertions and pytest output
  **不要使用print语句**：使用断言和pytest输出
- **Don't write tests that are too brittle**: Avoid over-specific mocks
  **不要写过于脆弱的测试**：避免过度具体的模拟

## Common Patterns

## English

### Testing API Endpoints (FastAPI/Flask)

## 中文

### 测试API端点 (FastAPI/Flask)

```python
@pytest.fixture
def client():
    app = create_app(testing=True)
    return app.test_client()

def test_get_user(client):
    response = client.get("/api/users/1")
    assert response.status_code == 200
    assert response.json["id"] == 1

def test_create_user(client):
    response = client.post("/api/users", json={
        "name": "Alice",
        "email": "alice@example.com"
    })
    assert response.status_code == 201
    assert response.json["name"] == "Alice"
```

### Testing Database Operations

## English

## 中文

### 测试数据库操作

```python
@pytest.fixture
def db_session():
    """Create a test database session."""
    # 创建测试数据库会话
    session = Session(bind=engine)
    session.begin_nested()
    yield session
    session.rollback()
    session.close()

def test_create_user(db_session):
    user = User(name="Alice", email="alice@example.com")
    db_session.add(user)
    db_session.commit()

    retrieved = db_session.query(User).filter_by(name="Alice").first()
    assert retrieved.email == "alice@example.com"
```

### Testing Class Methods

## English

## 中文

### 测试类方法

```python
class TestCalculator:
    @pytest.fixture
    def calculator(self):
        return Calculator()

    def test_add(self, calculator):
        assert calculator.add(2, 3) == 5

    def test_divide_by_zero(self, calculator):
        with pytest.raises(ZeroDivisionError):
            calculator.divide(10, 0)
```

## pytest Configuration

## English

### pytest.ini

## 中文

### pytest.ini

```ini
[pytest]
testpaths = tests
python_files = test_*.py
python_classes = Test*
python_functions = test_*
addopts =
    --strict-markers
    --disable-warnings
    --cov=mypackage
    --cov-report=term-missing
    --cov-report=html
markers =
    slow: marks tests as slow
    integration: marks tests as integration tests
    unit: marks tests as unit tests
```

### pyproject.toml

## English

## 中文

### pyproject.toml

```toml
[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py"]
python_classes = ["Test*"]
python_functions = ["test_*"]
addopts = [
    "--strict-markers",
    "--cov=mypackage",
    "--cov-report=term-missing",
    "--cov-report=html",
]
markers = [
    "slow: marks tests as slow",
    "integration: marks tests as integration tests",
    "unit: marks tests as unit tests",
]
```

## Running Tests

## English

## 中文

### 运行测试

```bash
# Run all tests
# 运行所有测试
pytest

# Run specific file
# 运行特定文件
pytest tests/test_utils.py

# Run specific test
# 运行特定测试
pytest tests/test_utils.py::test_function

# Run with verbose output
# 带详细输出运行
pytest -v

# Run with coverage
# 带覆盖率运行
pytest --cov=mypackage --cov-report=html

# Run only fast tests
# 只运行快速测试
pytest -m "not slow"

# Run until first failure
# 运行直到第一次失败
pytest -x

# Run and stop on N failures
# 运行并在N次失败后停止
pytest --maxfail=3

# Run last failed tests
# 运行上次失败的测试
pytest --lf

# Run tests with pattern
# 按模式运行测试
pytest -k "test_user"

# Run with debugger on failure
# 失败时使用调试器运行
pytest --pdb
```

## Quick Reference

## English

## 中文

### 快速参考

| Pattern | Usage |
|---------|-------|
| `pytest.raises()` | Test expected exceptions 测试预期异常 |
| `@pytest.fixture()` | Create reusable test fixtures 创建可重用的测试fixtures |
| `@pytest.mark.parametrize()` | Run tests with multiple inputs 使用多个输入运行测试 |
| `@pytest.mark.slow` | Mark slow tests 标记慢速测试 |
| `pytest -m "not slow"` | Skip slow tests 跳过慢速测试 |
| `@patch()` | Mock functions and classes 模拟函数和类 |
| `tmp_path` fixture | Automatic temp directory 自动临时目录 |
| `pytest --cov` | Generate coverage report 生成覆盖率报告 |
| `assert` | Simple and readable assertions 简单易读的断言 |

**Remember**: Tests are code too. Keep them clean, readable, and maintainable. Good tests catch bugs; great tests prevent them.

**记住**：测试也是代码。保持它们简洁、可读和可维护。好的测试能捕获bug；伟大的测试能防止bug。
