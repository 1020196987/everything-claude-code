---
name: perl-testing
description: Perl testing patterns using Test2::V0, Test::More, prove runner, mocking, coverage with Devel::Cover, and TDD methodology.
description zh-CN: 使用 Test2::V0、Test::More、prove 运行器、mock、Devel::Cover 覆盖率以及 TDD 方法论的 Perl 测试模式。
origin: ECC
---

# Perl Testing Patterns

## Perl Testing Patterns
## Perl 测试模式

Comprehensive testing strategies for Perl applications using Test2::V0, Test::More, prove, and TDD methodology.
使用 Test2::V0、Test::More、prove 和 TDD 方法论进行 Perl 应用程序的全面测试策略。

## When to Activate

## When to Activate
## 何时激活

- Writing new Perl code (follow TDD: red, green, refactor)
- 编写新的 Perl 代码（遵循 TDD：红、绿、重构）
- Designing test suites for Perl modules or applications
- 为 Perl 模块或应用程序设计测试套件
- Reviewing Perl test coverage
- 审查 Perl 测试覆盖率
- Setting up Perl testing infrastructure
- 设置 Perl 测试基础设施
- Migrating tests from Test::More to Test2::V0
- 将测试从 Test::More 迁移到 Test2::V0
- Debugging failing Perl tests
- 调试失败的 Perl 测试

## TDD Workflow

## TDD Workflow
## TDD 工作流

Always follow the RED-GREEN-REFACTOR cycle.
始终遵循 RED-GREEN-REFACTOR（红-绿-重构）循环。

```perl
# Step 1: RED — Write a failing test
# 步骤 1：RED — 写一个失败的测试
# t/unit/calculator.t
use v5.36;
use Test2::V0;

use lib 'lib';
use Calculator;

subtest 'addition' => sub {
    my $calc = Calculator->new;
    is($calc->add(2, 3), 5, 'adds two numbers');
    is($calc->add(-1, 1), 0, 'handles negatives');
};

done_testing;

# Step 2: GREEN — Write minimal implementation
# 步骤 2：GREEN — 写最少的实现
# lib/Calculator.pm
package Calculator;
use v5.36;
use Moo;

sub add($self, $a, $b) {
    return $a + $b;
}

1;

# Step 3: REFACTOR — Improve while tests stay green
# 步骤 3：REFACTOR — 在测试保持绿色时改进
# Run: prove -lv t/unit/calculator.t
```

## Test::More Fundamentals

## Test::More Fundamentals
## Test::More 基础

The standard Perl testing module — widely used, ships with core.
标准的 Perl 测试模块 — 被广泛使用，随 core 一起发布。

### Basic Assertions

### Basic Assertions
### 基本断言

```perl
use v5.36;
use Test::More;

# Plan upfront or use done_testing
# 提前计划或使用 done_testing
# plan tests => 5;  # Fixed plan (optional) / 固定计划（可选）

# Equality
# 相等性
is($result, 42, 'returns correct value');
isnt($result, 0, 'not zero');

# Boolean
# 布尔值
ok($user->is_active, 'user is active');
ok(!$user->is_banned, 'user is not banned');

# Deep comparison
# 深度比较
is_deeply(
    $got,
    { name => 'Alice', roles => ['admin'] },
    'returns expected structure'
);

# Pattern matching
# 模式匹配
like($error, qr/not found/i, 'error mentions not found');
unlike($output, qr/password/, 'output hides password');

# Type check
# 类型检查
isa_ok($obj, 'MyApp::User');
can_ok($obj, 'save', 'delete');

done_testing;
```

### SKIP and TODO

### SKIP and TODO
### SKIP 和 TODO

```perl
use v5.36;
use Test::More;

# Skip tests conditionally
# 条件性跳过测试
SKIP: {
    skip 'No database configured', 2 unless $ENV{TEST_DB};

    my $db = connect_db();
    ok($db->ping, 'database is reachable');
    is($db->version, '15', 'correct PostgreSQL version');
}

# Mark expected failures
# 标记预期失败
TODO: {
    local $TODO = 'Caching not yet implemented';
    is($cache->get('key'), 'value', 'cache returns value');
}

done_testing;
```

## Test2::V0 Modern Framework

## Test2::V0 Modern Framework
## Test2::V0 现代框架

Test2::V0 is the modern replacement for Test::More — richer assertions, better diagnostics, and extensible.
Test2::V0 是 Test::More 的现代替代品 — 更丰富的断言、更好的诊断和可扩展性。

### Why Test2?

### Why Test2?
### 为什么选择 Test2？

- Superior deep comparison with hash/array builders
- 使用哈希/数组构建器进行卓越的深度比较
- Better diagnostic output on failures
- 失败时更好的诊断输出
- Subtests with cleaner scoping
- 更清晰作用域的子测试
- Extensible via Test2::Tools::* plugins
- 通过 Test2::Tools::* 插件可扩展
- Backward-compatible with Test::More tests
- 向后兼容 Test::More 测试

### Deep Comparison with Builders

### Deep Comparison with Builders
### 使用构建器进行深度比较

```perl
use v5.36;
use Test2::V0;

# Hash builder — check partial structure
# 哈希构建器 — 检查部分结构
is(
    $user->to_hash,
    hash {
        field name  => 'Alice';
        field email => match(qr/\@example\.com$/);
        field age   => validator(sub { $_ >= 18 });
        # Ignore other fields / 忽略其他字段
        etc();
    },
    'user has expected fields'
);

# Array builder
# 数组构建器
is(
    $result,
    array {
        item 'first';
        item match(qr/^second/);
        item DNE();  # Does Not Exist — verify no extra items / 不存在 — 验证没有额外项
    },
    'result matches expected list'
);

# Bag — order-independent comparison
# Bag — 顺序无关比较
is(
    $tags,
    bag {
        item 'perl';
        item 'testing';
        item 'tdd';
    },
    'has all required tags regardless of order'
);
```

### Subtests

### Subtests
### 子测试

```perl
use v5.36;
use Test2::V0;

subtest 'User creation' => sub {
    my $user = User->new(name => 'Alice', email => 'alice@example.com');
    ok($user, 'user object created');
    is($user->name, 'Alice', 'name is set');
    is($user->email, 'alice@example.com', 'email is set');
};

subtest 'User validation' => sub {
    my $warnings = warns {
        User->new(name => '', email => 'bad');
    };
    ok($warnings, 'warns on invalid data');
};

done_testing;
```

### Exception Testing with Test2

### Exception Testing with Test2
### 使用 Test2 进行异常测试

```perl
use v5.36;
use Test2::V0;

# Test that code dies
# 测试代码是否死亡
like(
    dies { divide(10, 0) },
    qr/Division by zero/,
    'dies on division by zero'
);

# Test that code lives
# 测试代码是否存活
ok(lives { divide(10, 2) }, 'division succeeds') or note($@);

# Combined pattern
# 组合模式
subtest 'error handling' => sub {
    ok(lives { parse_config('valid.json') }, 'valid config parses');
    like(
        dies { parse_config('missing.json') },
        qr/Cannot open/,
        'missing file dies with message'
    );
};

done_testing;
```

## Test Organization and prove

## Test Organization and prove
## 测试组织和 prove

### Directory Structure

### Directory Structure
### 目录结构

```text
t/
├── 00-load.t              # Verify modules compile / 验证模块编译
├── 01-basic.t             # Core functionality / 核心功能
├── unit/
│   ├── config.t           # Unit tests by module / 按模块的单元测试
│   ├── user.t
│   └── util.t
├── integration/
│   ├── database.t
│   └── api.t
├── lib/
│   └── TestHelper.pm      # Shared test utilities / 共享测试工具
└── fixtures/
    ├── config.json        # Test data files / 测试数据文件
    └── users.csv
```

### prove Commands

### prove Commands
### prove 命令

```bash
# Run all tests
# 运行所有测试
prove -l t/

# Verbose output
# 详细输出
prove -lv t/

# Run specific test
# 运行特定测试
prove -lv t/unit/user.t

# Recursive search
# 递归搜索
prove -lr t/

# Parallel execution (8 jobs)
# 并行执行（8 个任务）
prove -lr -j8 t/

# Run only failing tests from last run
# 仅运行上次运行的失败测试
prove -l --state=failed t/

# Colored output with timer
# 带计时器的彩色输出
prove -l --color --timer t/

# TAP output for CI
# CI 的 TAP 输出
prove -l --formatter TAP::Formatter::JUnit t/ > results.xml
```

### .proverc Configuration

### .proverc Configuration
### .proverc 配置

```text
-l
--color
--timer
-r
-j4
--state=save
```

## Fixtures and Setup/Teardown

## Fixtures and Setup/Teardown
## Fixtures 和设置/拆卸

### Subtest Isolation

### Subtest Isolation
### 子测试隔离

```perl
use v5.36;
use Test2::V0;
use File::Temp qw(tempdir);
use Path::Tiny;

subtest 'file processing' => sub {
    # Setup / 设置
    my $dir = tempdir(CLEANUP => 1);
    my $file = path($dir, 'input.txt');
    $file->spew_utf8("line1\nline2\nline3\n");

    # Test / 测试
    my $result = process_file("$file");
    is($result->{line_count}, 3, 'counts lines');

    # Teardown happens automatically (CLEANUP => 1)
    # 拆卸自动发生（CLEANUP => 1）
};
```

### Shared Test Helpers

### Shared Test Helpers
### 共享测试辅助函数

Place reusable helpers in `t/lib/TestHelper.pm` and load with `use lib 't/lib'`. Export factory functions like `create_test_db()`, `create_temp_dir()`, and `fixture_path()` via `Exporter`.
将可重用的辅助函数放在 `t/lib/TestHelper.pm` 中，并使用 `use lib 't/lib'` 加载。通过 `Exporter` 导出工厂函数如 `create_test_db()`、`create_temp_dir()` 和 `fixture_path()`。

## Mocking

## Mocking
## Mock

### Test::MockModule

### Test::MockModule
### Test::MockModule

```perl
use v5.36;
use Test2::V0;
use Test::MockModule;

subtest 'mock external API' => sub {
    my $mock = Test::MockModule->new('MyApp::API');

    # Good: Mock returns controlled data
    # 好：Mock 返回受控数据
    $mock->mock(fetch_user => sub ($self, $id) {
        return { id => $id, name => 'Mock User', email => 'mock@test.com' };
    });

    my $api = MyApp::API->new;
    my $user = $api->fetch_user(42);
    is($user->{name}, 'Mock User', 'returns mocked user');

    # Verify call count
    # 验证调用次数
    my $call_count = 0;
    $mock->mock(fetch_user => sub { $call_count++; return {} });
    $api->fetch_user(1);
    $api->fetch_user(2);
    is($call_count, 2, 'fetch_user called twice');

    # Mock is automatically restored when $mock goes out of scope
    # Mock 在 $mock 超出作用域时自动恢复
};

# Bad: Monkey-patching without restoration
# 坏：没有恢复的猴子补丁
# *MyApp::API::fetch_user = sub { ... };  # NEVER — leaks across tests / 绝对不要 — 在测试间泄露
```

For lightweight mock objects, use `Test::MockObject` to create injectable test doubles with `->mock()` and verify calls with `->called_ok()`.
对于轻量级 mock 对象，使用 `Test::MockObject` 通过 `->mock()` 创建可注入的测试 double，并使用 `->called_ok()` 验证调用。

## Coverage with Devel::Cover

## Coverage with Devel::Cover
## 使用 Devel::Cover 进行覆盖率

### Running Coverage

### Running Coverage
### 运行覆盖率

```bash
# Basic coverage report
# 基本覆盖率报告
cover -test

# Or step by step
# 或分步进行
perl -MDevel::Cover -Ilib t/unit/user.t
cover

# HTML report
# HTML 报告
cover -report html
open cover_db/coverage.html

# Specific thresholds
# 特定阈值
cover -test -report text | grep 'Total'

# CI-friendly: fail under threshold
# CI 友好：低于阈值时失败
cover -test && cover -report text -select '^lib/' \
  | perl -ne 'if (/Total.*?(\d+\.\d+)/) { exit 1 if $1 < 80 }'
```

### Integration Testing

### Integration Testing
### 集成测试

Use in-memory SQLite for database tests, mock HTTP::Tiny for API tests.
数据库测试使用内存 SQLite，API 测试 mock HTTP::Tiny。

```perl
use v5.36;
use Test2::V0;
use DBI;

subtest 'database integration' => sub {
    my $dbh = DBI->connect('dbi:SQLite:dbname=:memory:', '', '', {
        RaiseError => 1,
    });
    $dbh->do('CREATE TABLE users (id INTEGER PRIMARY KEY, name TEXT)');

    $dbh->prepare('INSERT INTO users (name) VALUES (?)')->execute('Alice');
    my $row = $dbh->selectrow_hashref('SELECT * FROM users WHERE name = ?', undef, 'Alice');
    is($row->{name}, 'Alice', 'inserted and retrieved user');
};

done_testing;
```

## Best Practices

## Best Practices
## 最佳实践

### DO

### DO
### 应该

- **Follow TDD**: Write tests before implementation (red-green-refactor)
- **遵循 TDD**：在实现前写测试（红-绿-重构）
- **Use Test2::V0**: Modern assertions, better diagnostics
- **使用 Test2::V0**：现代断言，更好的诊断
- **Use subtests**: Group related assertions, isolate state
- **使用子测试**：分组相关断言，隔离状态
- **Mock external dependencies**: Network, database, file system
- **Mock 外部依赖**：网络、数据库、文件系统
- **Use `prove -l`**: Always include lib/ in `@INC`
- **使用 `prove -l`**：始终将 lib/ 包含在 `@INC` 中
- **Name tests clearly**: `'user login with invalid password fails'`
- **清晰命名测试**：`'user login with invalid password fails'`
- **Test edge cases**: Empty strings, undef, zero, boundary values
- **测试边界情况**：空字符串、undef、零、边界值
- **Aim for 80%+ coverage**: Focus on business logic paths
- **目标 80%+ 覆盖率**：专注于业务逻辑路径
- **Keep tests fast**: Mock I/O, use in-memory databases
- **保持测试快速**：Mock I/O，使用内存数据库

### DON'T

### DON'T
### 不应该

- **Don't test implementation**: Test behavior and output, not internals
- **不要测试实现**：测试行为和输出，而非内部实现
- **Don't share state between subtests**: Each subtest should be independent
- **不要在子测试间共享状态**：每个子测试应该是独立的
- **Don't skip `done_testing`**: Ensures all planned tests ran
- **不要跳过 `done_testing`**：确保所有计划的测试都运行了
- **Don't over-mock**: Mock boundaries only, not the code under test
- **不要过度 Mock**：只 Mock 边界，不要 Mock 被测代码
- **Don't use `Test::More` for new projects**: Prefer Test2::V0
- **不要在新项目中使用 `Test::More`**：优先使用 Test2::V0
- **Don't ignore test failures**: All tests must pass before merge
- **不要忽略测试失败**：合并前所有测试必须通过
- **Don't test CPAN modules**: Trust libraries to work correctly
- **不要测试 CPAN 模块**：信任库能正常工作
- **Don't write brittle tests**: Avoid over-specific string matching
- **不要写脆弱的测试**：避免过于具体的字符串匹配

## Quick Reference

## Quick Reference
## 快速参考

| Task | Command / Pattern |
| 任务 | 命令 / 模式 |
|---|---|
| Run all tests / 运行所有测试 | `prove -lr t/` |
| Run one test verbose / 运行单个测试详细 | `prove -lv t/unit/user.t` |
| Parallel test run / 并行测试运行 | `prove -lr -j8 t/` |
| Coverage report / 覆盖率报告 | `cover -test && cover -report html` |
| Test equality / 测试相等性 | `is($got, $expected, 'label')` |
| Deep comparison / 深度比较 | `is($got, hash { field k => 'v'; etc() }, 'label')` |
| Test exception / 测试异常 | `like(dies { ... }, qr/msg/, 'label')` |
| Test no exception / 测试无异常 | `ok(lives { ... }, 'label')` |
| Mock a method / Mock 一个方法 | `Test::MockModule->new('Pkg')->mock(m => sub { ... })` |
| Skip tests / 跳过测试 | `SKIP: { skip 'reason', $count unless $cond; ... }` |
| TODO tests / TODO 测试 | `TODO: { local $TODO = 'reason'; ... }` |

## Common Pitfalls

## Common Pitfalls
## 常见陷阱

### Forgetting `done_testing`

### Forgetting `done_testing`
### 忘记 `done_testing`

```perl
# Bad: Test file runs but doesn't verify all tests executed
# 坏：测试文件运行但不验证所有测试都执行了
use Test2::V0;
is(1, 1, 'works');
# Missing done_testing — silent bugs if test code is skipped
# 缺少 done_testing — 如果测试代码被跳过则静默出错

# Good: Always end with done_testing
# 好：始终以 done_testing 结束
use Test2::V0;
is(1, 1, 'works');
done_testing;
```

### Missing `-l` Flag

### Missing `-l` Flag
### 缺少 `-l` 标志

```bash
# Bad: Modules in lib/ not found
# 坏：找不到 lib/ 中的模块
prove t/unit/user.t
# Can't locate MyApp/User.pm in @INC

# Good: Include lib/ in @INC
# 好：将 lib/ 包含在 @INC 中
prove -l t/unit/user.t
```

### Over-Mocking

### Over-Mocking
### 过度 Mock

Mock the *dependency*, not the code under test. If your test only verifies that a mock returns what you told it to, it tests nothing.
Mock *依赖项*，而不是被测代码。如果你的测试只验证 mock 返回了你告诉它的内容，那它什么也没测。

### Test Pollution

### Test Pollution
### 测试污染

Use `my` variables inside subtests — never `our` — to prevent state leaking between tests.
在子测试中使用 `my` 变量 — 绝不要用 `our` — 以防止状态在测试间泄露。

**Remember**: Tests are your safety net. Keep them fast, focused, and independent. Use Test2::V0 for new projects, prove for running, and Devel::Cover for accountability.
**请记住**：测试是你的安全网。保持测试快速、专注和独立。新项目使用 Test2::V0，用 prove 运行测试，用 Devel::Cover 进行覆盖率追踪。
