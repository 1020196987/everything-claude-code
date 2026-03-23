---
name: perl-security
description: Comprehensive Perl security covering taint mode, input validation, safe process execution, DBI parameterized queries, web security (XSS/SQLi/CSRF), and perlcritic security policies.
description zh-CN: 全面的 Perl 安全指南，涵盖污染模式、输入验证、安全进程执行、DBI 参数化查询、Web 安全（XSS/SQLi/CSRF）和 perlcritic 安全策略。
origin: ECC
---

# Perl Security Patterns

## Perl Security Patterns
## Perl 安全模式

Comprehensive security guidelines for Perl applications covering input validation, injection prevention, and secure coding practices.
Perl 应用程序的全面安全指南，涵盖输入验证、注入防护和安全编码实践。

## When to Activate

## When to Activate
## 何时激活

- Handling user input in Perl applications
- 在 Perl 应用程序中处理用户输入
- Building Perl web applications (CGI, Mojolicious, Dancer2, Catalyst)
- 构建 Perl Web 应用程序（CGI, Mojolicious, Dancer2, Catalyst）
- Reviewing Perl code for security vulnerabilities
- 审查 Perl 代码的安全漏洞
- Performing file operations with user-supplied paths
- 使用用户提供的路径执行文件操作
- Executing system commands from Perl
- 从 Perl 执行系统命令
- Writing DBI database queries
- 编写 DBI 数据库查询

## How It Works

## How It Works
## 工作原理

Start with taint-aware input boundaries, then move outward: validate and untaint inputs, keep filesystem and process execution constrained, and use parameterized DBI queries everywhere. The examples below show the safe defaults this skill expects you to apply before shipping Perl code that touches user input, the shell, or the network.
从污染感知的输入边界开始，然后向外扩展：验证和取消输入的污染、保持文件系统和进程执行的约束、在各处使用参数化 DBI 查询。以下示例展示了此技能期望你在发布涉及用户输入、shell 或网络的 Perl 代码之前应用的安全默认值。

## Taint Mode

## Taint Mode
## 污染模式

Perl's taint mode (`-T`) tracks data from external sources and prevents it from being used in unsafe operations without explicit validation.
Perl 的污染模式（`-T`）跟踪来自外部源的数据，并防止其在未经显式验证的情况下用于不安全操作。

### Enabling Taint Mode

### Enabling Taint Mode
### 启用污染模式

```perl
#!/usr/bin/perl -T
use v5.36;

# Tainted: anything from outside the program
# 污染：来自程序外部的任何数据
my $input    = $ARGV[0];        # Tainted / 污染
my $env_path = $ENV{PATH};      # Tainted / 污染
my $form     = <STDIN>;         # Tainted / 污染
my $query    = $ENV{QUERY_STRING}; # Tainted / 污染

# Sanitize PATH early (required in taint mode)
# 尽早清理 PATH（污染模式必需）
$ENV{PATH} = '/usr/local/bin:/usr/bin:/bin';
delete @ENV{qw(IFS CDPATH ENV BASH_ENV)};
```

### Untainting Pattern

### Untainting Pattern
### 取消污染模式

```perl
use v5.36;

# Good: Validate and untaint with a specific regex
# 好：使用特定正则表达式验证和取消污染
sub untaint_username($input) {
    if ($input =~ /^([a-zA-Z0-9_]{3,30})$/) {
        return $1;  # $1 is untainted / $1 是干净的
    }
    die "Invalid username: must be 3-30 alphanumeric characters\n";
}

# Good: Validate and untaint a file path
# 好：验证和取消污染文件路径
sub untaint_filename($input) {
    if ($input =~ m{^([a-zA-Z0-9._-]+)$}) {
        return $1;
    }
    die "Invalid filename: contains unsafe characters\n";
}

# Bad: Overly permissive untainting (defeats the purpose)
# 坏：过度宽松的取消污染（违背目的）
sub bad_untaint($input) {
    $input =~ /^(.*)$/s;
    return $1;  # Accepts ANYTHING — pointless / 接受任何内容 — 无意义
}
```

## Input Validation

## Input Validation
## 输入验证

### Allowlist Over Blocklist

### Allowlist Over Blocklist
### 白名单优于黑名单

```perl
use v5.36;

# Good: Allowlist — define exactly what's permitted
# 好：白名单 — 精确定义允许的内容
sub validate_sort_field($field) {
    my %allowed = map { $_ => 1 } qw(name email created_at updated_at);
    die "Invalid sort field: $field\n" unless $allowed{$field};
    return $field;
}

# Good: Validate with specific patterns
# 好：使用特定模式验证
sub validate_email($email) {
    if ($email =~ /^([a-zA-Z0-9._%+-]+\@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,})$/) {
        return $1;
    }
    die "Invalid email address\n";
}

sub validate_integer($input) {
    if ($input =~ /^(-?\d{1,10})$/) {
        return $1 + 0;  # Coerce to number / 强制转换为数字
    }
    die "Invalid integer\n";
}

# Bad: Blocklist — always incomplete
# 坏：黑名单 — 总是不完整
sub bad_validate($input) {
    die "Invalid" if $input =~ /[<>"';&|]/;  # Misses encoded attacks / 会遗漏编码攻击
    return $input;
}
```

### Length Constraints

### Length Constraints
### 长度限制

```perl
use v5.36;

sub validate_comment($text) {
    die "Comment is required\n"        unless length($text) > 0;
    die "Comment exceeds 10000 chars\n" if length($text) > 10_000;
    return $text;
}
```

## Safe Regular Expressions

## Safe Regular Expressions
## 安全正则表达式

### ReDoS Prevention

### ReDoS Prevention
### ReDoS 防护

Catastrophic backtracking occurs with nested quantifiers on overlapping patterns.
嵌套量词在重叠模式上会导致灾难性回溯。

```perl
use v5.36;

# Bad: Vulnerable to ReDoS (exponential backtracking)
# 坏：易受 ReDoS 攻击（指数级回溯）
my $bad_re = qr/^(a+)+$/;           # Nested quantifiers / 嵌套量词
my $bad_re2 = qr/^([a-zA-Z]+)*$/;   # Nested quantifiers on class / 类的嵌套量词
my $bad_re3 = qr/^(.*?,){10,}$/;    # Repeated greedy/lazy combo / 重复贪婪/惰性组合

# Good: Rewrite without nesting
# 好：重写为无嵌套
my $good_re = qr/^a+$/;             # Single quantifier / 单个量词
my $good_re2 = qr/^[a-zA-Z]+$/;     # Single quantifier on class / 类的单个量词

# Good: Use possessive quantifiers or atomic groups to prevent backtracking
# 好：使用占有量词或原子组防止回溯
my $safe_re = qr/^[a-zA-Z]++$/;             # Possessive (5.10+) / 占有量词（5.10+）
my $safe_re2 = qr/^(?>a+)$/;                # Atomic group / 原子组

# Good: Enforce timeout on untrusted patterns
# 好：对不可信模式强制超时
use POSIX qw(alarm);
sub safe_match($string, $pattern, $timeout = 2) {
    my $matched;
    eval {
        local $SIG{ALRM} = sub { die "Regex timeout\n" };
        alarm($timeout);
        $matched = $string =~ $pattern;
        alarm(0);
    };
    alarm(0);
    die $@ if $@;
    return $matched;
}
```

## Safe File Operations

## Safe File Operations
## 安全文件操作

### Three-Argument Open

### Three-Argument Open
### 三参数 open

```perl
use v5.36;

# Good: Three-arg open, lexical filehandle, check return
# 好：三参数 open，词法文件句柄，检查返回值
sub read_file($path) {
    open my $fh, '<:encoding(UTF-8)', $path
        or die "Cannot open '$path': $!\n";
    local $/;
    my $content = <$fh>;
    close $fh;
    return $content;
}

# Bad: Two-arg open with user data (command injection)
# 坏：用户数据的两参数 open（命令注入）
sub bad_read($path) {
    open my $fh, $path;        # If $path = "|rm -rf /", runs command! / 如果 $path = "|rm -rf /"，执行命令！
    open my $fh, "< $path";   # Shell metacharacter injection / shell 元字符注入
}
```

### TOCTOU Prevention and Path Traversal

### TOCTOU Prevention and Path Traversal
### TOCTOU 防护和路径遍历

```perl
use v5.36;
use Fcntl qw(:DEFAULT :flock);
use File::Spec;
use Cwd qw(realpath);

# Atomic file creation
# 原子文件创建
sub create_file_safe($path) {
    sysopen(my $fh, $path, O_WRONLY | O_CREAT | O_EXCL, 0600)
        or die "Cannot create '$path': $!\n";
    return $fh;
}

# Validate path stays within allowed directory
# 验证路径保持在允许的目录内
sub safe_path($base_dir, $user_path) {
    my $real = realpath(File::Spec->catfile($base_dir, $user_path))
        // die "Path does not exist\n";
    my $base_real = realpath($base_dir)
        // die "Base dir does not exist\n";
    die "Path traversal blocked\n" unless $real =~ /^\Q$base_real\E(?:\/|\z)/;
    return $real;
}
```

Use `File::Temp` for temporary files (`tempfile(UNLINK => 1)`) and `flock(LOCK_EX)` to prevent race conditions.
使用 `File::Temp` 处理临时文件（`tempfile(UNLINK => 1)`）和 `flock(LOCK_EX)` 防止竞态条件。

## Safe Process Execution

## Safe Process Execution
## 安全进程执行

### List-Form system and exec

### List-Form system and exec
### 列表形式 system 和 exec

```perl
use v5.36;

# Good: List form — no shell interpolation
# 好：列表形式 — 无 shell 插值
sub run_command(@cmd) {
    system(@cmd) == 0
        or die "Command failed: @cmd\n";
}

run_command('grep', '-r', $user_pattern, '/var/log/app/');

# Good: Capture output safely with IPC::Run3
# 好：使用 IPC::Run3 安全捕获输出
use IPC::Run3;
sub capture_output(@cmd) {
    my ($stdout, $stderr);
    run3(\@cmd, \undef, \$stdout, \$stderr);
    if ($?) {
        die "Command failed (exit $?): $stderr\n";
    }
    return $stdout;
}

# Bad: String form — shell injection!
# 坏：字符串形式 — shell 注入！
sub bad_search($pattern) {
    system("grep -r '$pattern' /var/log/app/");  # If $pattern = "'; rm -rf / #"
}

# Bad: Backticks with interpolation
# 坏：带插值的反引号
my $output = `ls $user_dir`;   # Shell injection risk / shell 注入风险
```

Also use `Capture::Tiny` for capturing stdout/stderr from external commands safely.
也可使用 `Capture::Tiny` 安全捕获外部命令的 stdout/stderr。

## SQL Injection Prevention

## SQL Injection Prevention
## SQL 注入防护

### DBI Placeholders

### DBI Placeholders
### DBI 占位符

```perl
use v5.36;
use DBI;

my $dbh = DBI->connect($dsn, $user, $pass, {
    RaiseError => 1,
    PrintError => 0,
    AutoCommit => 1,
});

# Good: Parameterized queries — always use placeholders
# 好：参数化查询 — 始终使用占位符
sub find_user($dbh, $email) {
    my $sth = $dbh->prepare('SELECT * FROM users WHERE email = ?');
    $sth->execute($email);
    return $sth->fetchrow_hashref;
}

sub search_users($dbh, $name, $status) {
    my $sth = $dbh->prepare(
        'SELECT * FROM users WHERE name LIKE ? AND status = ? ORDER BY name'
    );
    $sth->execute("%$name%", $status);
    return $sth->fetchall_arrayref({});
}

# Bad: String interpolation in SQL (SQLi vulnerability!)
# 坏：SQL 中的字符串插值（SQLi 漏洞！）
sub bad_find($dbh, $email) {
    my $sth = $dbh->prepare("SELECT * FROM users WHERE email = '$email'");
    # If $email = "' OR 1=1 --", returns all users / 如果 $email = "' OR 1=1 --"，返回所有用户
    $sth->execute;
    return $sth->fetchrow_hashref;
}
```

### Dynamic Column Allowlists

### Dynamic Column Allowlists
### 动态列白名单

```perl
use v5.36;

# Good: Validate column names against an allowlist
# 好：对照白名单验证列名
sub order_by($dbh, $column, $direction) {
    my %allowed_cols = map { $_ => 1 } qw(name email created_at);
    my %allowed_dirs = map { $_ => 1 } qw(ASC DESC);

    die "Invalid column: $column\n"    unless $allowed_cols{$column};
    die "Invalid direction: $direction\n" unless $allowed_dirs{uc $direction};

    my $sth = $dbh->prepare("SELECT * FROM users ORDER BY $column $direction");
    $sth->execute;
    return $sth->fetchall_arrayref({});
}

# Bad: Directly interpolating user-chosen column
# 坏：直接插值用户选择的列
sub bad_order($dbh, $column) {
    $dbh->prepare("SELECT * FROM users ORDER BY $column");  # SQLi! / SQLi！
}
```

### DBIx::Class (ORM Safety)

### DBIx::Class (ORM Safety)
### DBIx::Class（ORM 安全）

```perl
use v5.36;

# DBIx::Class generates safe parameterized queries
# DBIx::Class 生成安全的参数化查询
my @users = $schema->resultset('User')->search({
    status => 'active',
    email  => { -like => '%@example.com' },
}, {
    order_by => { -asc => 'name' },
    rows     => 50,
});
```

## Web Security

## Web Security
## Web 安全

### XSS Prevention

### XSS Prevention
### XSS 防护

```perl
use v5.36;
use HTML::Entities qw(encode_entities);
use URI::Escape qw(uri_escape_utf8);

# Good: Encode output for HTML context
# 好：为 HTML 上下文编码输出
sub safe_html($user_input) {
    return encode_entities($user_input);
}

# Good: Encode for URL context
# 好：为 URL 上下文编码
sub safe_url_param($value) {
    return uri_escape_utf8($value);
}

# Good: Encode for JSON context
# 好：为 JSON 上下文编码
use JSON::MaybeXS qw(encode_json);
sub safe_json($data) {
    return encode_json($data);  # Handles escaping / 处理转义
}

# Template auto-escaping (Mojolicious)
# 模板自动转义（Mojolicious）
# <%= $user_input %>   — auto-escaped (safe) / 自动转义（安全）
# <%== $raw_html %>    — raw output (dangerous, use only for trusted content) / 原始输出（危险，仅用于可信内容）

# Template auto-escaping (Template Toolkit)
# 模板自动转义（Template Toolkit）
# [% user_input | html %]  — explicit HTML encoding / 显式 HTML 编码

# Bad: Raw output in HTML
# 坏：HTML 中的原始输出
sub bad_html($input) {
    print "<div>$input</div>";  # XSS if $input contains <script> / 如果 $input 包含 <script> 则 XSS
}
```

### CSRF Protection

### CSRF Protection
### CSRF 防护

```perl
use v5.36;
use Crypt::URandom qw(urandom);
use MIME::Base64 qw(encode_base64url);

sub generate_csrf_token() {
    return encode_base64url(urandom(32));
}
```

Use constant-time comparison when verifying tokens. Most web frameworks (Mojolicious, Dancer2, Catalyst) provide built-in CSRF protection — prefer those over hand-rolled solutions.
验证令牌时使用恒定时间比较。大多数 Web 框架（Mojolicious, Dancer2, Catalyst）都提供内置 CSRF 防护 — 优先使用而非自己编写。

### Session and Header Security

### Session and Header Security
### 会话和响应头安全

```perl
use v5.36;

# Mojolicious session + headers
# Mojolicious 会话 + 响应头
$app->secrets(['long-random-secret-rotated-regularly']);
$app->sessions->secure(1);          # HTTPS only / 仅 HTTPS
$app->sessions->samesite('Lax');

$app->hook(after_dispatch => sub ($c) {
    $c->res->headers->header('X-Content-Type-Options' => 'nosniff');
    $c->res->headers->header('X-Frame-Options'        => 'DENY');
    $c->res->headers->header('Content-Security-Policy' => "default-src 'self'");
    $c->res->headers->header('Strict-Transport-Security' => 'max-age=31536000; includeSubDomains');
});
```

## Output Encoding

## Output Encoding
## 输出编码

Always encode output for its context: `HTML::Entities::encode_entities()` for HTML, `URI::Escape::uri_escape_utf8()` for URLs, `JSON::MaybeXS::encode_json()` for JSON.
始终根据上下文对输出进行编码：HTML 用 `HTML::Entities::encode_entities()`、URL 用 `URI::Escape::uri_escape_utf8()`、JSON 用 `JSON::MaybeXS::encode_json()`。

## CPAN Module Security

## CPAN Module Security
## CPAN 模块安全

- **Pin versions** in cpanfile: `requires 'DBI', '== 1.643';`
- **在 cpanfile 中固定版本**：`requires 'DBI', '== 1.643';`
- **Prefer maintained modules**: Check MetaCPAN for recent releases
- **优先选择维护中的模块**：查看 MetaCPAN 的最新发布
- **Minimize dependencies**: Each dependency is an attack surface
- **最小化依赖**：每个依赖都是攻击面

## Security Tooling

## Security Tooling
## 安全工具

### perlcritic Security Policies

### perlcritic Security Policies
### perlcritic 安全策略

```ini
# .perlcriticrc — security-focused configuration
# .perlcriticrc — 安全配置
severity = 3
theme = security + core

# Require three-arg open
# 要求三参数 open
[InputOutput::RequireThreeArgOpen]
severity = 5

# Require checked system calls
# 要求检查系统调用
[InputOutput::RequireCheckedSyscalls]
functions = :builtins
severity = 4

# Prohibit string eval
# 禁止字符串 eval
[BuiltinFunctions::ProhibitStringyEval]
severity = 5

# Prohibit backtick operators
# 禁止反引号运算符
[InputOutput::ProhibitBacktickOperators]
severity = 4

# Require taint checking in CGI
# CGI 中要求污染检查
[Modules::RequireTaintChecking]
severity = 5

# Prohibit two-arg open
# 禁止两参数 open
[InputOutput::ProhibitTwoArgOpen]
severity = 5

# Prohibit bare-word filehandles
# 禁止裸词文件句柄
[InputOutput::ProhibitBarewordFileHandles]
severity = 5
```

### Running perlcritic

### Running perlcritic
### 运行 perlcritic

```bash
# Check a file
# 检查文件
perlcritic --severity 3 --theme security lib/MyApp/Handler.pm

# Check entire project
# 检查整个项目
perlcritic --severity 3 --theme security lib/

# CI integration
# CI 集成
perlcritic --severity 4 --theme security --quiet lib/ || exit 1
```

## Quick Security Checklist

## Quick Security Checklist
## 快速安全检查清单

| Check | What to Verify |
| 检查项 | 验证内容 |
|---|---|
| Taint mode | `-T` flag on CGI/web scripts / CGI/Web 脚本的 `-T` 标志 |
| Input validation | Allowlist patterns, length limits / 白名单模式，长度限制 |
| File operations | Three-arg open, path traversal checks / 三参数 open，路径遍历检查 |
| Process execution | List-form system, no shell interpolation / 列表形式 system，无 shell 插值 |
| SQL queries | DBI placeholders, never interpolate / DBI 占位符，绝不插值 |
| HTML output | `encode_entities()`, template auto-escape / 模板自动转义 |
| CSRF tokens | Generated, verified on state-changing requests / 生成，在状态变更请求时验证 |
| Session config | Secure, HttpOnly, SameSite cookies / 安全、HttpOnly、SameSite cookie |
| HTTP headers | CSP, X-Frame-Options, HSTS |
| Dependencies | Pinned versions, audited modules / 固定版本，审查模块 |
| Regex safety | No nested quantifiers, anchored patterns / 无嵌套量词，有锚定模式 |
| Error messages | No stack traces or paths leaked to users / 不向用户泄露堆栈跟踪或路径 |

## Anti-Patterns

## Anti-Patterns
## 反模式

```perl
# 1. Two-arg open with user data (command injection)
# 1. 用户数据的两参数 open（命令注入）
open my $fh, $user_input;               # CRITICAL vulnerability / 严重漏洞

# 2. String-form system (shell injection)
# 2. 字符串形式 system（shell 注入）
system("convert $user_file output.png"); # CRITICAL vulnerability / 严重漏洞

# 3. SQL string interpolation
# 3. SQL 字符串插值
$dbh->do("DELETE FROM users WHERE id = $id");  # SQLi / SQL 注入

# 4. eval with user input (code injection)
# 4. 带用户输入的 eval（代码注入）
eval $user_code;                         # Remote code execution / 远程代码执行

# 5. Trusting $ENV without sanitizing
# 5. 信任 $ENV 而不清理
my $path = $ENV{UPLOAD_DIR};             # Could be manipulated / 可能被操纵
system("ls $path");                      # Double vulnerability / 双重漏洞

# 6. Disabling taint without validation
# 6. 禁用污染而不验证
($input) = $input =~ /(.*)/s;           # Lazy untaint — defeats purpose / 惰性取消污染 — 违背目的

# 7. Raw user data in HTML
# 7. HTML 中的原始用户数据
print "<div>Welcome, $username!</div>";  # XSS

# 8. Unvalidated redirects
# 8. 未验证的重定向
print $cgi->redirect($user_url);         # Open redirect / 开放重定向
```

**Remember**: Perl's flexibility is powerful but requires discipline. Use taint mode for web-facing code, validate all input with allowlists, use DBI placeholders for every query, and encode all output for its context. Defense in depth — never rely on a single layer.
**请记住**：Perl 的灵活性很强大，但需要纪律性。对 Web 相关代码使用污染模式，使用白名单验证所有输入，对每个查询使用 DBI 占位符，并根据上下文编码所有输出。深度防御 — 绝不依赖单一层。
