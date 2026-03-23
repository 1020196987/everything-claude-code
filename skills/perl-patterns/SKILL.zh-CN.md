---
name: perl-patterns
description: Modern Perl 5.36+ idioms, best practices, and conventions for building robust, maintainable Perl applications.
description zh-CN: 现代 Perl 5.36+ 惯用法、最佳实践和约定，用于构建健壮、可维护的 Perl 应用。
origin: ECC
---

# Modern Perl Development Patterns

## Modern Perl Development Patterns
## 现代 Perl 开发模式

Idiomatic Perl 5.36+ patterns and best practices for building robust, maintainable applications.
用于构建健壮、可维护应用的惯用 Perl 5.36+ 模式和最佳实践。

## When to Activate

## When to Activate
## 何时激活

- Writing new Perl code or modules
- 编写新的 Perl 代码或模块
- Reviewing Perl code for idiom compliance
- 审查 Perl 代码是否符合惯用法
- Refactoring legacy Perl to modern standards
- 将遗留 Perl 重构为现代标准
- Designing Perl module architecture
- 设计 Perl 模块架构
- Migrating pre-5.36 code to modern Perl
- 将 5.36 之前的代码迁移到现代 Perl

## How It Works

## How It Works
## 工作原理

Apply these patterns as a bias toward modern Perl 5.36+ defaults: signatures, explicit modules, focused error handling, and testable boundaries. The examples below are meant to be copied as starting points, then tightened for the actual app, dependency stack, and deployment model in front of you.
将这些模式作为现代 Perl 5.36+ 默认值的偏好来应用：签名、显式模块、专注的错误处理和可测试的边界。以下示例旨在作为起点复制，然后根据实际应用、依赖栈和部署模型进行调整。

## Core Principles

## Core Principles
## 核心原则

### 1. Use `v5.36` Pragma

### 1. Use `v5.36` Pragma
### 1. 使用 `v5.36` 编译指令

A single `use v5.36` replaces the old boilerplate and enables strict, warnings, and subroutine signatures.
单个 `use v5.36` 替代了旧的样板代码，并启用 strict、warnings 和子程序签名。

```perl
# Good: Modern preamble
# 好：现代序言
use v5.36;

sub greet($name) {
    say "Hello, $name!";
}

# Bad: Legacy boilerplate
# 坏：遗留样板代码
use strict;
use warnings;
use feature 'say', 'signatures';
no warnings 'experimental::signatures';

sub greet {
    my ($name) = @_;
    say "Hello, $name!";
}
```

### 2. Subroutine Signatures

### 2. Subroutine Signatures
### 2. 子程序签名

Use signatures for clarity and automatic arity checking.
使用签名以提高清晰度并自动进行参数个数检查。

```perl
use v5.36;

# Good: Signatures with defaults
# 好：带默认值的签名
sub connect_db($host, $port = 5432, $timeout = 30) {
    # $host is required, others have defaults
    # $host 是必需的，其他有默认值
    return DBI->connect("dbi:Pg:host=$host;port=$port", undef, undef, {
        RaiseError => 1,
        PrintError => 0,
    });
}

# Good: Slurpy parameter for variable args
# 好：可变参数的 slurpy 参数
sub log_message($level, @details) {
    say "[$level] " . join(' ', @details);
}

# Bad: Manual argument unpacking
# 坏：手动参数解包
sub connect_db {
    my ($host, $port, $timeout) = @_;
    $port    //= 5432;
    $timeout //= 30;
    # ...
}
```

### 3. Context Sensitivity

### 3. Context Sensitivity
### 3. 上下文敏感性

Understand scalar vs list context — a core Perl concept.
理解标量上下文与列表上下文的区别 — 这是 Perl 的核心概念。

```perl
use v5.36;

my @items = (1, 2, 3, 4, 5);

my @copy  = @items;            # List context: all elements
my $count = @items;            # Scalar context: count (5)
say "Items: " . scalar @items; # Force scalar context
```

### 4. Postfix Dereferencing

### 4. Postfix Dereferencing
### 4. 后缀解引用

Use postfix dereference syntax for readability with nested structures.
对嵌套结构使用后缀解引用语法以提高可读性。

```perl
use v5.36;

my $data = {
    users => [
        { name => 'Alice', roles => ['admin', 'user'] },
        { name => 'Bob',   roles => ['user'] },
    ],
};

# Good: Postfix dereferencing
# 好：后缀解引用
my @users = $data->{users}->@*;
my @roles = $data->{users}[0]{roles}->@*;
my %first = $data->{users}[0]->%*;

# Bad: Circumfix dereferencing (harder to read in chains)
# 坏：环缀解引用（链式使用时更难阅读）
my @users = @{ $data->{users} };
my @roles = @{ $data->{users}[0]{roles} };
```

### 5. The `isa` Operator (5.32+)

### 5. The `isa` Operator (5.32+)
### 5. `isa` 运算符（5.32+）

Infix type-check — replaces `blessed($o) && $o->isa('X')`.
中缀类型检查 — 替代 `blessed($o) && $o->isa('X')`。

```perl
use v5.36;
if ($obj isa 'My::Class') { $obj->do_something }
```

## Error Handling

## Error Handling
## 错误处理

### eval/die Pattern

### eval/die Pattern
### eval/die 模式

```perl
use v5.36;

sub parse_config($path) {
    my $content = eval { path($path)->slurp_utf8 };
    die "Config error: $@" if $@;
    return decode_json($content);
}
```

### Try::Tiny (Reliable Exception Handling)

### Try::Tiny (Reliable Exception Handling)
### Try::Tiny（可靠的异常处理）

```perl
use v5.36;
use Try::Tiny;

sub fetch_user($id) {
    my $user = try {
        $db->resultset('User')->find($id)
            // die "User $id not found\n";
    }
    catch {
        warn "Failed to fetch user $id: $_";
        undef;
    };
    return $user;
}
```

### Native try/catch (5.40+)

### Native try/catch (5.40+)
### 原生 try/catch（5.40+）

```perl
use v5.40;

sub divide($x, $y) {
    try {
        die "Division by zero" if $y == 0;
        return $x / $y;
    }
    catch ($e) {
        warn "Error: $e";
        return;
    }
}
```

## Modern OO with Moo

## Modern OO with Moo
## 使用 Moo 实现现代 OO

Prefer Moo for lightweight, modern OO. Use Moose only when its metaprotocol is needed.
轻量级现代 OO 首选 Moo。仅在需要元协议时才使用 Moose。

```perl
# Good: Moo class
# 好：Moo 类
package User;
use Moo;
use Types::Standard qw(Str Int ArrayRef);
use namespace::autoclean;

has name  => (is => 'ro', isa => Str, required => 1);
has email => (is => 'ro', isa => Str, required => 1);
has age   => (is => 'ro', isa => Int, default  => sub { 0 });
has roles => (is => 'ro', isa => ArrayRef[Str], default => sub { [] });

sub is_admin($self) {
    return grep { $_ eq 'admin' } $self->roles->@*;
}

sub greet($self) {
    return "Hello, I'm " . $self->name;
}

1;

# Usage
# 用法
my $user = User->new(
    name  => 'Alice',
    email => 'alice@example.com',
    roles => ['admin', 'user'],
);

# Bad: Blessed hashref (no validation, no accessors)
# 坏：blessed 哈希引用（无验证，无访问器）
package User;
sub new {
    my ($class, %args) = @_;
    return bless \%args, $class;
}
sub name { return $_[0]->{name} }
1;
```

### Moo Roles

### Moo Roles
### Moo 角色

```perl
package Role::Serializable;
use Moo::Role;
use JSON::MaybeXS qw(encode_json);
requires 'TO_HASH';
sub to_json($self) { encode_json($self->TO_HASH) }
1;

package User;
use Moo;
with 'Role::Serializable';
has name  => (is => 'ro', required => 1);
has email => (is => 'ro', required => 1);
sub TO_HASH($self) { { name => $self->name, email => $self->email } }
1;
```

### Native `class` Keyword (5.38+, Corinna)

### Native `class` Keyword (5.38+, Corinna)
### 原生 `class` 关键字（5.38+，Corinna）

```perl
use v5.38;
use feature 'class';
no warnings 'experimental::class';

class Point {
    field $x :param;
    field $y :param;
    method magnitude() { sqrt($x**2 + $y**2) }
}

my $p = Point->new(x => 3, y => 4);
say $p->magnitude;  # 5
```

## Regular Expressions

## Regular Expressions
## 正则表达式

### Named Captures and `/x` Flag

### Named Captures and `/x` Flag
### 命名捕获和 `/x` 标志

```perl
use v5.36;

# Good: Named captures with /x for readability
# 好：使用 /x 的命名捕获以提高可读性
my $log_re = qr{
    ^ (?<timestamp> \d{4}-\d{2}-\d{2} \s \d{2}:\d{2}:\d{2} )
    \s+ \[ (?<level> \w+ ) \]
    \s+ (?<message> .+ ) $
}x;

if ($line =~ $log_re) {
    say "Time: $+{timestamp}, Level: $+{level}";
    say "Message: $+{message}";
}

# Bad: Positional captures (hard to maintain)
# 坏：位置捕获（难以维护）
if ($line =~ /^(\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2})\s+\[(\w+)\]\s+(.+)$/) {
    say "Time: $1, Level: $2";
}
```

### Precompiled Patterns

### Precompiled Patterns
### 预编译模式

```perl
use v5.36;

# Good: Compile once, use many
# 好：编译一次，多次使用
my $email_re = qr/^[A-Za-z0-9._%+-]+\@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$/;

sub validate_emails(@emails) {
    return grep { $_ =~ $email_re } @emails;
}
```

## Data Structures

## Data Structures
## 数据结构

### References and Safe Deep Access

### References and Safe Deep Access
### 引用和安全深度访问

```perl
use v5.36;

# Hash and array references
# 哈希和数组引用
my $config = {
    database => {
        host => 'localhost',
        port => 5432,
        options => ['utf8', 'sslmode=require'],
    },
};

# Safe deep access (returns undef if any level missing)
# 安全深度访问（任意层级缺失时返回 undef）
my $port = $config->{database}{port};           # 5432
my $missing = $config->{cache}{host};           # undef, no error

# Hash slices
# 哈希切片
my %subset;
@subset{qw(host port)} = @{$config->{database}}{qw(host port)};

# Array slices
# 数组切片
my @first_two = $config->{database}{options}->@[0, 1];

# Multi-variable for loop (experimental in 5.36, stable in 5.40)
# 多变量 for 循环（5.36 中为实验性，5.40 中稳定）
use feature 'for_list';
no warnings 'experimental::for_list';
for my ($key, $val) (%$config) {
    say "$key => $val";
}
```

## File I/O

## File I/O
## 文件 I/O

### Three-Argument Open

### Three-Argument Open
### 三参数 open

```perl
use v5.36;

# Good: Three-arg open with autodie (core module, eliminates 'or die')
# 好：带 autodie 的三参数 open（核心模块，省略 'or die'）
use autodie;

sub read_file($path) {
    open my $fh, '<:encoding(UTF-8)', $path;
    local $/;
    my $content = <$fh>;
    close $fh;
    return $content;
}

# Bad: Two-arg open (shell injection risk, see perl-security)
# 坏：两参数 open（shell 注入风险，参见 perl-security）
open FH, $path;            # NEVER do this
open FH, "< $path";        # Still bad — user data in mode string
```

### Path::Tiny for File Operations

### Path::Tiny for File Operations
### Path::Tiny 文件操作

```perl
use v5.36;
use Path::Tiny;

my $file = path('config', 'app.json');
my $content = $file->slurp_utf8;
$file->spew_utf8($new_content);

# Iterate directory
# 遍历目录
for my $child (path('src')->children(qr/\.pl$/)) {
    say $child->basename;
}
```

## Module Organization

## Module Organization
## 模块组织

### Standard Project Layout

### Standard Project Layout
### 标准项目布局

```text
MyApp/
├── lib/
│   └── MyApp/
│       ├── App.pm           # Main module / 主模块
│       ├── Config.pm        # Configuration / 配置
│       ├── DB.pm            # Database layer / 数据库层
│       └── Util.pm          # Utilities / 工具
├── bin/
│   └── myapp                # Entry-point script / 入口脚本
├── t/
│   ├── 00-load.t            # Compilation tests / 编译测试
│   ├── unit/                # Unit tests / 单元测试
│   └── integration/         # Integration tests / 集成测试
├── cpanfile                 # Dependencies / 依赖
├── Makefile.PL              # Build system / 构建系统
└── .perlcriticrc            # Linting config / 代码检查配置
```

### Exporter Patterns

### Exporter Patterns
### Exporter 模式

```perl
package MyApp::Util;
use v5.36;
use Exporter 'import';

our @EXPORT_OK   = qw(trim);
our %EXPORT_TAGS = (all => \@EXPORT_OK);

sub trim($str) { $str =~ s/^\s+|\s+$//gr }

1;
```

## Tooling

## Tooling
## 工具

### perltidy Configuration (.perltidyrc)

### perltidy Configuration (.perltidyrc)
### perltidy 配置（.perltidyrc）

```text
-i=4        # 4-space indent / 4 空格缩进
-l=100      # 100-char line length / 100 字符行长度
-ci=4       # continuation indent / 续行缩进
-ce         # cuddled else / else 紧跟
-bar        # opening brace on same line / 左花括号同行
-nolq       # don't outdent long quoted strings / 不突出长引号字符串
```

### perlcritic Configuration (.perlcriticrc)

### perlcritic Configuration (.perlcriticrc)
### perlcritic 配置（.perlcriticrc）

```ini
severity = 3
theme = core + pbp + security

[InputOutput::RequireCheckedSyscalls]
functions = :builtins
exclude_functions = say print

[Subroutines::ProhibitExplicitReturnUndef]
severity = 4

[ValuesAndExpressions::ProhibitMagicNumbers]
allowed_values = 0 1 2 -1
```

### Dependency Management (cpanfile + carton)

### Dependency Management (cpanfile + carton)
### 依赖管理（cpanfile + carton）

```bash
cpanm App::cpanminus Carton   # Install tools / 安装工具
carton install                 # Install deps from cpanfile / 从 cpanfile 安装依赖
carton exec -- perl bin/myapp  # Run with local deps / 使用本地依赖运行
```

```perl
# cpanfile
requires 'Moo', '>= 2.005';
requires 'Path::Tiny';
requires 'JSON::MaybeXS';
requires 'Try::Tiny';

on test => sub {
    requires 'Test2::V0';
    requires 'Test::MockModule';
};
```

## Quick Reference: Modern Perl Idioms

## Quick Reference: Modern Perl Idioms
## 快速参考：现代 Perl 惯用法

| Legacy Pattern | Modern Replacement |
| 遗留模式 | 现代替代方案 |
|---|---|
| `use strict; use warnings;` | `use v5.36;` |
| `my ($x, $y) = @_;` | `sub foo($x, $y) { ... }` |
| `@{ $ref }` | `$ref->@*` |
| `%{ $ref }` | `$ref->%*` |
| `open FH, "< $file"` | `open my $fh, '<:encoding(UTF-8)', $file` |
| `blessed hashref` | `Moo` class with types |
| `$1, $2, $3` | `$+{name}` (named captures) |
| `eval { }; if ($@)` | `Try::Tiny` or native `try/catch` (5.40+) |
| `BEGIN { require Exporter; }` | `use Exporter 'import';` |
| Manual file ops | `Path::Tiny` |
| `blessed($o) && $o->isa('X')` | `$o isa 'X'` (5.32+) |
| `builtin::true / false` | `use builtin 'true', 'false';` (5.36+, experimental) |

## Anti-Patterns

## Anti-Patterns
## 反模式

```perl
# 1. Two-arg open (security risk)
# 1. 两参数 open（安全风险）
open FH, $filename;                     # NEVER / 绝对不要

# 2. Indirect object syntax (ambiguous parsing)
# 2. 间接对象语法（解析歧义）
my $obj = new Foo(bar => 1);            # Bad / 坏
my $obj = Foo->new(bar => 1);           # Good / 好

# 3. Excessive reliance on $_
# 3. 过度依赖 $_
map { process($_) } grep { validate($_) } @items;  # Hard to follow / 难以追踪
my @valid = grep { validate($_) } @items;           # Better: break it up / 更好：拆开
my @results = map { process($_) } @valid;

# 4. Disabling strict refs
# 4. 禁用 strict refs
no strict 'refs';                        # Almost always wrong / 几乎总是错误的
${"My::Package::$var"} = $value;         # Use a hash instead / 改用哈希

# 5. Global variables as configuration
# 5. 全局变量作为配置
our $TIMEOUT = 30;                       # Bad: mutable global / 坏：可变全局变量
use constant TIMEOUT => 30;              # Better: constant / 更好：常量
# Best: Moo attribute with default / 最佳：Moo 属性带默认值

# 6. String eval for module loading
# 6. 字符串 eval 加载模块
eval "require $module";                  # Bad: code injection risk / 坏：代码注入风险
eval "use $module";                      # Bad / 坏
use Module::Runtime 'require_module';    # Good: safe module loading / 好：安全模块加载
require_module($module);
```

**Remember**: Modern Perl is clean, readable, and safe. Let `use v5.36` handle the boilerplate, use Moo for objects, and prefer CPAN's battle-tested modules over hand-rolled solutions.
**请记住**：现代 Perl 是简洁、可读和安全的。让 `use v5.36` 处理样板代码，使用 Moo 实现对象，并优先选择 CPAN 中经过实战检验的模块而非自己编写的解决方案。
