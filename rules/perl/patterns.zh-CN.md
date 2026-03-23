---
name: Perl Patterns
description: Perl Patterns
description: Perl 设计模式和惯用法
paths:
  - "**/*.pl"
  - "**/*.pm"
  - "**/*.t"
  - "**/*.psgi"
  - "**/*.cgi"
---
# Perl Patterns
# Perl 模式

> This file extends [common/patterns.md](../common/patterns.md) with Perl-specific content.
> 此文件扩展了 [common/patterns.md](../common/patterns.md)，包含 Perl 特定内容。

## Repository Pattern
## 仓储模式

Use **DBI** or **DBIx::Class** behind an interface:
在接口后面使用 **DBI** 或 **DBIx::Class**：

```perl
package MyApp::Repo::User;
use Moo;

has dbh => (is => 'ro', required => 1);

sub find_by_id ($self, $id) {
    my $sth = $self->dbh->prepare('SELECT * FROM users WHERE id = ?');
    $sth->execute($id);
    return $sth->fetchrow_hashref;
}
```

## DTOs / Value Objects
## DTO / 值对象

Use **Moo** classes with **Types::Standard** (equivalent to Python dataclasses):
使用 **Moo** 类配合 **Types::Standard**（相当于 Python dataclasses）：

```perl
package MyApp::DTO::User;
use Moo;
use Types::Standard qw(Str Int);

has name  => (is => 'ro', isa => Str, required => 1);
has email => (is => 'ro', isa => Str, required => 1);
has age   => (is => 'ro', isa => Int);
```

## Resource Management
## 资源管理

- Always use **three-arg open** with `autodie`
- 始终使用**三参数 open** 配合 `autodie`
- Use **Path::Tiny** for file operations
- 使用 **Path::Tiny** 进行文件操作

```perl
use autodie;
use Path::Tiny;

my $content = path('config.json')->slurp_utf8;
```

## Module Interface
## 模块接口

Use `Exporter 'import'` with `@EXPORT_OK` — never `@EXPORT`:
使用 `Exporter 'import'` 配合 `@EXPORT_OK` — 永不使用 `@EXPORT`：

```perl
use Exporter 'import';
our @EXPORT_OK = qw(parse_config validate_input);
```

## Dependency Management
## 依赖管理

Use **cpanfile** + **carton** for reproducible installs:
使用 **cpanfile** + **carton** 进行可重现安装：

```bash
carton install
carton exec prove -lr t/
```

## Reference
## 参考

See skill: `perl-patterns` for comprehensive modern Perl patterns and idioms.
参见 skill: `perl-patterns` 了解全面的现代 Perl 模式和惯用法。
