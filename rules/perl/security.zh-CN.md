---
name: Perl Security
description: Perl Security
description: Perl 安全指南
paths:
  - "**/*.pl"
  - "**/*.pm"
  - "**/*.t"
  - "**/*.psgi"
  - "**/*.cgi"
---
# Perl Security
# Perl 安全

> This file extends [common/security.md](../common/security.md) with Perl-specific content.
> 此文件扩展了 [common/security.md](../common/security.md)，包含 Perl 特定内容。

## Taint Mode
## 污染模式

- Use `-T` flag on all CGI/web-facing scripts
- 在所有 CGI/面向 Web 的脚本上使用 `-T` 标志
- Sanitize `%ENV` (`$ENV{PATH}`, `$ENV{CDPATH}`, etc.) before any external command
- 在任何外部命令之前清理 `%ENV`（`$ENV{PATH}`、`$ENV{CDPATH}` 等）

## Input Validation
## 输入验证

- Use allowlist regex for untainting — never `/(.*)/s`
- 使用白名单 regex 去除污染 — 永不使用 `/(.*)/s`
- Validate all user input with explicit patterns:
- 使用显式模式验证所有用户输入：

```perl
if ($input =~ /\A([a-zA-Z0-9_-]+)\z/) {
    my $clean = $1;
}
```

## File I/O
## 文件 I/O

- **Three-arg open only** — never two-arg open
- **仅使用三参数 open** — 永不使用两参数 open
- Prevent path traversal with `Cwd::realpath`:
- 使用 `Cwd::realpath` 防止路径遍历：

```perl
use Cwd 'realpath';
my $safe_path = realpath($user_path);
die "Path traversal" unless $safe_path =~ m{\A/allowed/directory/};
```

## Process Execution
## 进程执行

- Use **list-form `system()`** — never single-string form
- 使用**列表形式 `system()`** — 永不使用单字符串形式
- Use **IPC::Run3** for capturing output
- 使用 **IPC::Run3** 捕获输出
- Never use backticks with variable interpolation
- 永不在带变量插值的情况下使用反引号

```perl
system('grep', '-r', $pattern, $directory);  # safe
```

## SQL Injection Prevention
## SQL 注入防护

Always use DBI placeholders — never interpolate into SQL:
始终使用 DBI 占位符 — 永不在 SQL 中插值：

```perl
my $sth = $dbh->prepare('SELECT * FROM users WHERE email = ?');
$sth->execute($email);
```

## Security Scanning
## 安全扫描

Run **perlcritic** with the security theme at severity 4+:
使用严重级别 4+ 的安全主题运行 **perlcritic**：

```bash
perlcritic --severity 4 --theme security lib/
```

## Reference
## 参考

See skill: `perl-security` for comprehensive Perl security patterns, taint mode, and safe I/O.
参见 skill: `perl-security` 了解全面的 Perl 安全模式、污染模式和安全 I/O。
