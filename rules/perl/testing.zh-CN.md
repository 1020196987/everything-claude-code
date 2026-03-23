---
name: Perl Testing
description: Perl Testing
description: Perl 测试框架和覆盖率
paths:
  - "**/*.pl"
  - "**/*.pm"
  - "**/*.t"
  - "**/*.psgi"
  - "**/*.cgi"
---
# Perl Testing
# Perl 测试

> This file extends [common/testing.md](../common/testing.md) with Perl-specific content.
> 此文件扩展了 [common/testing.md](../common/testing.md)，包含 Perl 特定内容。

## Framework
## 框架

Use **Test2::V0** for new projects (not Test::More):
对新项目使用 **Test2::V0**（而非 Test::More）：

```perl
use Test2::V0;

is($result, 42, 'answer is correct');

done_testing;
```

## Runner
## 运行器

```bash
prove -l t/              # adds lib/ to @INC
prove -lr -j8 t/         # recursive, 8 parallel jobs
prove -l t/              # adds lib/ to @INC
prove -lr -j8 t/         # recursive, 8 parallel jobs
```

Always use `-l` to ensure `lib/` is on `@INC`.
始终使用 `-l` 以确保 `lib/` 在 `@INC` 中。

## Coverage
## 覆盖率

Use **Devel::Cover** — target 80%+:
使用 **Devel::Cover** — 目标是 80%+：

```bash
cover -test
```

## Mocking
## Mocking

- **Test::MockModule** — mock methods on existing modules
- **Test::MockModule** — 对现有模块进行 mock
- **Test::MockObject** — create test doubles from scratch
- **Test::MockObject** — 从头创建测试替身

## Pitfalls
## 陷阱

- Always end test files with `done_testing`
- 始终以 `done_testing` 结束测试文件
- Never forget the `-l` flag with `prove`
- 永不忘掉 `prove` 的 `-l` 标志

## Reference
## 参考

See skill: `perl-testing` for detailed Perl TDD patterns with Test2::V0, prove, and Devel::Cover.
参见 skill: `perl-testing` 了解详细的 Perl TDD 模式，包括 Test2::V0、prove 和 Devel::Cover。
