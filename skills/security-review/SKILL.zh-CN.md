---
name: security-review
description: Use this skill when adding authentication, handling user input, working with secrets, creating API endpoints, or implementing payment/sensitive features. Provides comprehensive security checklist and patterns.
description zh-CN: 在添加身份验证、处理用户输入、使用机密、创建 API 端点或实现支付/敏感功能时使用此技能。提供全面的安全检查清单和模式。
origin: ECC
---

# Security Review Skill
# 安全审查技能

This skill ensures all code follows security best practices and identifies potential vulnerabilities.
此技能确保所有代码遵循安全最佳实践并识别潜在漏洞。

## When to Activate
## 何时激活

- Implementing authentication or authorization
- 实现身份验证或授权
- Handling user input or file uploads
- 处理用户输入或文件上传
- Creating new API endpoints
- 创建新的 API 端点
- Working with secrets or credentials
- 使用机密或凭据
- Implementing payment features
- 实现支付功能
- Storing or transmitting sensitive data
- 存储或传输敏感数据
- Integrating third-party APIs
- 集成第三方 API

## Security Checklist
## 安全检查清单

### 1. Secrets Management
### 1. 机密管理

#### ❌ NEVER Do This
#### ❌ 永远不要这样做

```typescript
const apiKey = "sk-proj-xxxxx"  // Hardcoded secret
const dbPassword = "password123" // In source code
```

```typescript
const apiKey = "sk-proj-xxxxx"  // 硬编码的机密
const dbPassword = "password123" // 在源代码中
```

#### ✅ ALWAYS Do This
#### ✅ 始终这样做

```typescript
const apiKey = process.env.OPENAI_API_KEY
const dbUrl = process.env.DATABASE_URL

// Verify secrets exist
if (!apiKey) {
  throw new Error('OPENAI_API_KEY not configured')
}
```

```typescript
const apiKey = process.env.OPENAI_API_KEY
const dbUrl = process.env.DATABASE_URL

// 验证机密存在
if (!apiKey) {
  throw new Error('OPENAI_API_KEY not configured')
}
```

#### Verification Steps
#### 验证步骤

- [ ] No hardcoded API keys, tokens, or passwords
- [ ] 无硬编码的 API 密钥、令牌或密码
- [ ] All secrets in environment variables
- [ ] 所有机密在环境变量中
- [ ] `.env.local` in .gitignore
- [ ] `.env.local` 在 .gitignore 中
- [ ] No secrets in git history
- [ ] Git 历史中无机密
- [ ] Production secrets in hosting platform (Vercel, Railway)
- [ ] 生产机密在托管平台上（Vercel、 Railway）

### 2. Input Validation
### 2. 输入验证

#### Always Validate User Input
#### 始终验证用户输入

```typescript
import { z } from 'zod'

// Define validation schema
const CreateUserSchema = z.object({
  email: z.string().email(),
  name: z.string().min(1).max(100),
  age: z.number().int().min(0).max(150)
})

// Validate before processing
export async function createUser(input: unknown) {
  try {
    const validated = CreateUserSchema.parse(input)
    return await db.users.create(validated)
  } catch (error) {
    if (error instanceof z.ZodError) {
      return { success: false, errors: error.errors }
    }
    throw error
  }
}
```

```typescript
import { z } from 'zod'

// 定义验证模式
const CreateUserSchema = z.object({
  email: z.string().email(),
  name: z.string().min(1).max(100),
  age: z.number().int().min(0).max(150)
})

// 处理前验证
export async function createUser(input: unknown) {
  try {
    const validated = CreateUserSchema.parse(input)
    return await db.users.create(validated)
  } catch (error) {
    if (error instanceof z.ZodError) {
      return { success: false, errors: error.errors }
    }
    throw error
  }
}
```

#### File Upload Validation
#### 文件上传验证

```typescript
function validateFileUpload(file: File) {
  // Size check (5MB max)
  const maxSize = 5 * 1024 * 1024
  if (file.size > maxSize) {
    throw new Error('File too large (max 5MB)')
  }

  // Type check
  const allowedTypes = ['image/jpeg', 'image/png', 'image/gif']
  if (!allowedTypes.includes(file.type)) {
    throw new Error('Invalid file type')
  }

  // Extension check
  const allowedExtensions = ['.jpg', '.jpeg', '.png', '.gif']
  const extension = file.name.toLowerCase().match(/\.[^.]+$/)?.[0]
  if (!extension || !allowedExtensions.includes(extension)) {
    throw new Error('Invalid file extension')
  }

  return true
}
```

```typescript
function validateFileUpload(file: File) {
  // 大小检查（最大 5MB）
  const maxSize = 5 * 1024 * 1024
  if (file.size > maxSize) {
    throw new Error('File too large (max 5MB)')
  }

  // 类型检查
  const allowedTypes = ['image/jpeg', 'image/png', 'image/gif']
  if (!allowedTypes.includes(file.type)) {
    throw new Error('Invalid file type')
  }

  // 扩展名检查
  const allowedExtensions = ['.jpg', '.jpeg', '.png', '.gif']
  const extension = file.name.toLowerCase().match(/\.[^.]+$/)?.[0]
  if (!extension || !allowedExtensions.includes(extension)) {
    throw new Error('Invalid file extension')
  }

  return true
}
```

#### Verification Steps
#### 验证步骤

- [ ] All user inputs validated with schemas
- [ ] 所有用户输入都经过模式验证
- [ ] File uploads restricted (size, type, extension)
- [ ] 文件上传受限（大小、类型、扩展名）
- [ ] No direct use of user input in queries
- [ ] 查询中不直接使用用户输入
- [ ] Whitelist validation (not blacklist)
- [ ] 白名单验证（非黑名单）
- [ ] Error messages don't leak sensitive info
- [ ] 错误消息不泄露敏感信息

### 3. SQL Injection Prevention
### 3. SQL 注入预防

#### ❌ NEVER Concatenate SQL
#### ❌ 永远不要拼接 SQL

```typescript
// DANGEROUS - SQL Injection vulnerability
const query = `SELECT * FROM users WHERE email = '${userEmail}'`
await db.query(query)
```

```typescript
// 危险 - SQL 注入漏洞
const query = `SELECT * FROM users WHERE email = '${userEmail}'`
await db.query(query)
```

#### ✅ ALWAYS Use Parameterized Queries
#### ✅ 始终使用参数化查询

```typescript
// Safe - parameterized query
const { data } = await supabase
  .from('users')
  .select('*')
  .eq('email', userEmail)

// Or with raw SQL
await db.query(
  'SELECT * FROM users WHERE email = $1',
  [userEmail]
)
```

```typescript
// 安全 - 参数化查询
const { data } = await supabase
  .from('users')
  .select('*')
  .eq('email', userEmail)

// 或使用原始 SQL
await db.query(
  'SELECT * FROM users WHERE email = $1',
  [userEmail]
)
```

#### Verification Steps
#### 验证步骤

- [ ] All database queries use parameterized queries
- [ ] 所有数据库查询都使用参数化查询
- [ ] No string concatenation in SQL
- [ ] SQL 中没有字符串拼接
- [ ] ORM/query builder used correctly
- [ ] ORM/query builder 使用正确
- [ ] Supabase queries properly sanitized
- [ ] Supabase 查询正确清理

### 4. Authentication & Authorization
### 4. 身份验证与授权

#### JWT Token Handling
#### JWT 令牌处理

```typescript
// ❌ WRONG: localStorage (vulnerable to XSS)
localStorage.setItem('token', token)

// ✅ CORRECT: httpOnly cookies
res.setHeader('Set-Cookie',
  `token=${token}; HttpOnly; Secure; SameSite=Strict; Max-Age=3600`)
```

```typescript
// ❌ 错误：localStorage（容易受到 XSS 攻击）
localStorage.setItem('token', token)

// ✅ 正确：httpOnly cookies
res.setHeader('Set-Cookie',
  `token=${token}; HttpOnly; Secure; SameSite=Strict; Max-Age=3600`)
```

#### Authorization Checks
#### 授权检查

```typescript
export async function deleteUser(userId: string, requesterId: string) {
  // ALWAYS verify authorization first
  const requester = await db.users.findUnique({
    where: { id: requesterId }
  })

  if (requester.role !== 'admin') {
    return NextResponse.json(
      { error: 'Unauthorized' },
      { status: 403 }
    )
  }

  // Proceed with deletion
  await db.users.delete({ where: { id: userId } })
}
```

```typescript
export async function deleteUser(userId: string, requesterId: string) {
  // 始终首先验证授权
  const requester = await db.users.findUnique({
    where: { id: requesterId }
  })

  if (requester.role !== 'admin') {
    return NextResponse.json(
      { error: 'Unauthorized' },
      { status: 403 }
    )
  }

  // 继续删除
  await db.users.delete({ where: { id: userId } })
}
```

#### Row Level Security (Supabase)
#### 行级安全（Supabase）

```sql
-- Enable RLS on all tables
ALTER TABLE users ENABLE ROW LEVEL SECURITY;

-- Users can only view their own data
CREATE POLICY "Users view own data"
  ON users FOR SELECT
  USING (auth.uid() = id);

-- Users can only update their own data
CREATE POLICY "Users update own data"
  ON users FOR UPDATE
  USING (auth.uid() = id);
```

```sql
-- 在所有表上启用 RLS
ALTER TABLE users ENABLE ROW LEVEL SECURITY;

-- 用户只能查看自己的数据
CREATE POLICY "Users view own data"
  ON users FOR SELECT
  USING (auth.uid() = id);

-- 用户只能更新自己的数据
CREATE POLICY "Users update own data"
  ON users FOR UPDATE
  USING (auth.uid() = id);
```

#### Verification Steps
#### 验证步骤

- [ ] Tokens stored in httpOnly cookies (not localStorage)
- [ ] 令牌存储在 httpOnly cookies 中（非 localStorage）
- [ ] Authorization checks before sensitive operations
- [ ] 敏感操作前进行授权检查
- [ ] Row Level Security enabled in Supabase
- [ ] 在 Supabase 中启用行级安全
- [ ] Role-based access control implemented
- [ ] 实施基于角色的访问控制
- [ ] Session management secure
- [ ] 会话管理安全

### 5. XSS Prevention
### 5. XSS 预防

#### Sanitize HTML
#### 清理 HTML

```typescript
import DOMPurify from 'isomorphic-dompurify'

// ALWAYS sanitize user-provided HTML
function renderUserContent(html: string) {
  const clean = DOMPurify.sanitize(html, {
    ALLOWED_TAGS: ['b', 'i', 'em', 'strong', 'p'],
    ALLOWED_ATTR: []
  })
  return <div dangerouslySetInnerHTML={{ __html: clean }} />
}
```

```typescript
import DOMPurify from 'isomorphic-dompurify'

// 始终清理用户提供的 HTML
function renderUserContent(html: string) {
  const clean = DOMPurify.sanitize(html, {
    ALLOWED_TAGS: ['b', 'i', 'em', 'strong', 'p'],
    ALLOWED_ATTR: []
  })
  return <div dangerouslySetInnerHTML={{ __html: clean }} />
}
```

#### Content Security Policy
#### 内容安全策略

```typescript
// next.config.js
const securityHeaders = [
  {
    key: 'Content-Security-Policy',
    value: `
      default-src 'self';
      script-src 'self' 'unsafe-eval' 'unsafe-inline';
      style-src 'self' 'unsafe-inline';
      img-src 'self' data: https:;
      font-src 'self';
      connect-src 'self' https://api.example.com;
    `.replace(/\s{2,}/g, ' ').trim()
  }
]
```

```typescript
// next.config.js
const securityHeaders = [
  {
    key: 'Content-Security-Policy',
    value: `
      default-src 'self';
      script-src 'self' 'unsafe-eval' 'unsafe-inline';
      style-src 'self' 'unsafe-inline';
      img-src 'self' data: https:;
      font-src 'self';
      connect-src 'self' https://api.example.com;
    `.replace(/\s{2,}/g, ' ').trim()
  }
]
```

#### Verification Steps
#### 验证步骤

- [ ] User-provided HTML sanitized
- [ ] 用户提供的 HTML 已清理
- [ ] CSP headers configured
- [ ] 已配置 CSP headers
- [ ] No unvalidated dynamic content rendering
- [ ] 无未验证的动态内容渲染
- [ ] React's built-in XSS protection used
- [ ] 使用 React 内置的 XSS 保护

### 6. CSRF Protection
### 6. CSRF 保护

#### CSRF Tokens
#### CSRF 令牌

```typescript
import { csrf } from '@/lib/csrf'

export async function POST(request: Request) {
  const token = request.headers.get('X-CSRF-Token')

  if (!csrf.verify(token)) {
    return NextResponse.json(
      { error: 'Invalid CSRF token' },
      { status: 403 }
    )
  }

  // Process request
}
```

```typescript
import { csrf } from '@/lib/csrf'

export async function POST(request: Request) {
  const token = request.headers.get('X-CSRF-Token')

  if (!csrf.verify(token)) {
    return NextResponse.json(
      { error: 'Invalid CSRF token' },
      { status: 403 }
    )
  }

  // 处理请求
}
```

#### SameSite Cookies
#### SameSite Cookies

```typescript
res.setHeader('Set-Cookie',
  `session=${sessionId}; HttpOnly; Secure; SameSite=Strict`)
```

```typescript
res.setHeader('Set-Cookie',
  `session=${sessionId}; HttpOnly; Secure; SameSite=Strict`)
```

#### Verification Steps
#### 验证步骤

- [ ] CSRF tokens on state-changing operations
- [ ] 状态变更操作上有 CSRF 令牌
- [ ] SameSite=Strict on all cookies
- [ ] 所有 cookies 上使用 SameSite=Strict
- [ ] Double-submit cookie pattern implemented
- [ ] 已实现双重提交 cookie 模式

### 7. Rate Limiting
### 7. 速率限制

#### API Rate Limiting
#### API 速率限制

```typescript
import rateLimit from 'express-rate-limit'

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // 100 requests per window
  message: 'Too many requests'
})

// Apply to routes
app.use('/api/', limiter)
```

```typescript
import rateLimit from 'express-rate-limit'

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 分钟
  max: 100, // 每个窗口 100 个请求
  message: 'Too many requests'
})

// 应用于路由
app.use('/api/', limiter)
```

#### Expensive Operations
#### 昂贵操作

```typescript
// Aggressive rate limiting for searches
const searchLimiter = rateLimit({
  windowMs: 60 * 1000, // 1 minute
  max: 10, // 10 requests per minute
  message: 'Too many search requests'
})

app.use('/api/search', searchLimiter)
```

```typescript
// 对搜索进行严格的速率限制
const searchLimiter = rateLimit({
  windowMs: 60 * 1000, // 1 分钟
  max: 10, // 每分钟 10 个请求
  message: 'Too many search requests'
})

app.use('/api/search', searchLimiter)
```

#### Verification Steps
#### 验证步骤

- [ ] Rate limiting on all API endpoints
- [ ] 所有 API 端点都有速率限制
- [ ] Stricter limits on expensive operations
- [ ] 昂贵操作有更严格的限制
- [ ] IP-based rate limiting
- [ ] 基于 IP 的速率限制
- [ ] User-based rate limiting (authenticated)
- [ ] 基于用户的速率限制（已认证）

### 8. Sensitive Data Exposure
### 8. 敏感数据暴露

#### Logging
#### 日志记录

```typescript
// ❌ WRONG: Logging sensitive data
console.log('User login:', { email, password })
console.log('Payment:', { cardNumber, cvv })

// ✅ CORRECT: Redact sensitive data
console.log('User login:', { email, userId })
console.log('Payment:', { last4: card.last4, userId })
```

```typescript
// ❌ 错误：记录敏感数据
console.log('User login:', { email, password })
console.log('Payment:', { cardNumber, cvv })

// ✅ 正确：编辑敏感数据
console.log('User login:', { email, userId })
console.log('Payment:', { last4: card.last4, userId })
```

#### Error Messages
#### 错误消息

```typescript
// ❌ WRONG: Exposing internal details
catch (error) {
  return NextResponse.json(
    { error: error.message, stack: error.stack },
    { status: 500 }
  )
}

// ✅ CORRECT: Generic error messages
catch (error) {
  console.error('Internal error:', error)
  return NextResponse.json(
    { error: 'An error occurred. Please try again.' },
    { status: 500 }
  )
}
```

```typescript
// ❌ 错误：暴露内部细节
catch (error) {
  return NextResponse.json(
    { error: error.message, stack: error.stack },
    { status: 500 }
  )
}

// ✅ 正确：通用错误消息
catch (error) {
  console.error('Internal error:', error)
  return NextResponse.json(
    { error: 'An error occurred. Please try again.' },
    { status: 500 }
  )
}
```

#### Verification Steps
#### 验证步骤

- [ ] No passwords, tokens, or secrets in logs
- [ ] 日志中没有密码、令牌或机密
- [ ] Error messages generic for users
- [ ] 对用户使用通用错误消息
- [ ] Detailed errors only in server logs
- [ ] 详细错误仅在服务器日志中
- [ ] No stack traces exposed to users
- [ ] 不向用户暴露堆栈跟踪

### 9. Blockchain Security (Solana)
### 9. 区块链安全（Solana）

#### Wallet Verification
#### 钱包验证

```typescript
import { verify } from '@solana/web3.js'

async function verifyWalletOwnership(
  publicKey: string,
  signature: string,
  message: string
) {
  try {
    const isValid = verify(
      Buffer.from(message),
      Buffer.from(signature, 'base64'),
      Buffer.from(publicKey, 'base64')
    )
    return isValid
  } catch (error) {
    return false
  }
}
```

```typescript
import { verify } from '@solana/web3.js'

async function verifyWalletOwnership(
  publicKey: string,
  signature: string,
  message: string
) {
  try {
    const isValid = verify(
      Buffer.from(message),
      Buffer.from(signature, 'base64'),
      Buffer.from(publicKey, 'base64')
    )
    return isValid
  } catch (error) {
    return false
  }
}
```

#### Transaction Verification
#### 交易验证

```typescript
async function verifyTransaction(transaction: Transaction) {
  // Verify recipient
  if (transaction.to !== expectedRecipient) {
    throw new Error('Invalid recipient')
  }

  // Verify amount
  if (transaction.amount > maxAmount) {
    throw new Error('Amount exceeds limit')
  }

  // Verify user has sufficient balance
  const balance = await getBalance(transaction.from)
  if (balance < transaction.amount) {
    throw new Error('Insufficient balance')
  }

  return true
}
```

```typescript
async function verifyTransaction(transaction: Transaction) {
  // 验证接收者
  if (transaction.to !== expectedRecipient) {
    throw new Error('Invalid recipient')
  }

  // 验证金额
  if (transaction.amount > maxAmount) {
    throw new Error('Amount exceeds limit')
  }

  // 验证用户有足够的余额
  const balance = await getBalance(transaction.from)
  if (balance < transaction.amount) {
    throw new Error('Insufficient balance')
  }

  return true
}
```

#### Verification Steps
#### 验证步骤

- [ ] Wallet signatures verified
- [ ] 验证钱包签名
- [ ] Transaction details validated
- [ ] 验证交易详情
- [ ] Balance checks before transactions
- [ ] 交易前检查余额
- [ ] No blind transaction signing
- [ ] 不进行盲目交易签名

### 10. Dependency Security
### 10. 依赖安全

#### Regular Updates
#### 定期更新

```bash
# Check for vulnerabilities
npm audit

# Fix automatically fixable issues
npm audit fix

# Update dependencies
npm update

# Check for outdated packages
npm outdated
```

```bash
# 检查漏洞
npm audit

# 自动修复问题
npm audit fix

# 更新依赖
npm update

# 检查过时的包
npm outdated
```

#### Lock Files
#### 锁文件

```bash
# ALWAYS commit lock files
git add package-lock.json

# Use in CI/CD for reproducible builds
npm ci  # Instead of npm install
```

```bash
# 始终提交锁文件
git add package-lock.json

# 在 CI/CD 中使用以实现可重现的构建
npm ci  # 而不是 npm install
```

#### Verification Steps
#### 验证步骤

- [ ] Dependencies up to date
- [ ] 依赖是最新的
- [ ] No known vulnerabilities (npm audit clean)
- [ ] 无已知漏洞（npm audit clean）
- [ ] Lock files committed
- [ ] 锁文件已提交
- [ ] Dependabot enabled on GitHub
- [ ] 在 GitHub 上启用 Dependabot
- [ ] Regular security updates
- [ ] 定期安全更新

## Security Testing
## 安全测试

### Automated Security Tests
### 自动化安全测试

```typescript
// Test authentication
test('requires authentication', async () => {
  const response = await fetch('/api/protected')
  expect(response.status).toBe(401)
})

// Test authorization
test('requires admin role', async () => {
  const response = await fetch('/api/admin', {
    headers: { Authorization: `Bearer ${userToken}` }
  })
  expect(response.status).toBe(403)
})

// Test input validation
test('rejects invalid input', async () => {
  const response = await fetch('/api/users', {
    method: 'POST',
    body: JSON.stringify({ email: 'not-an-email' })
  })
  expect(response.status).toBe(400)
})

// Test rate limiting
test('enforces rate limits', async () => {
  const requests = Array(101).fill(null).map(() =>
    fetch('/api/endpoint')
  )

  const responses = await Promise.all(requests)
  const tooManyRequests = responses.filter(r => r.status === 429)

  expect(tooManyRequests.length).toBeGreaterThan(0)
})
```

```typescript
// 测试身份验证
test('requires authentication', async () => {
  const response = await fetch('/api/protected')
  expect(response.status).toBe(401)
})

// 测试授权
test('requires admin role', async () => {
  const response = await fetch('/api/admin', {
    headers: { Authorization: `Bearer ${userToken}` }
  })
  expect(response.status).toBe(403)
})

// 测试输入验证
test('rejects invalid input', async () => {
  const response = await fetch('/api/users', {
    method: 'POST',
    body: JSON.stringify({ email: 'not-an-email' })
  })
  expect(response.status).toBe(400)
})

// 测试速率限制
test('enforces rate limits', async () => {
  const requests = Array(101).fill(null).map(() =>
    fetch('/api/endpoint')
  )

  const responses = await Promise.all(requests)
  const tooManyRequests = responses.filter(r => r.status === 429)

  expect(tooManyRequests.length).toBeGreaterThan(0)
})
```

## Pre-Deployment Security Checklist
## 部署前安全检查清单

Before ANY production deployment:
在任何生产部署之前：

- [ ] **Secrets**: No hardcoded secrets, all in env vars
- [ ] **机密**：无硬编码机密，全部在环境变量中
- [ ] **Input Validation**: All user inputs validated
- [ ] **输入验证**：所有用户输入都已验证
- [ ] **SQL Injection**: All queries parameterized
- [ ] **SQL 注入**：所有查询都是参数化的
- [ ] **XSS**: User content sanitized
- [ ] **XSS**：用户内容已清理
- [ ] **CSRF**: Protection enabled
- [ ] **CSRF**：保护已启用
- [ ] **Authentication**: Proper token handling
- [ ] **身份验证**：正确的令牌处理
- [ ] **Authorization**: Role checks in place
- [ ] **授权**：角色检查到位
- [ ] **Rate Limiting**: Enabled on all endpoints
- [ ] **速率限制**：所有端点都启用了速率限制
- [ ] **HTTPS**: Enforced in production
- [ ] **HTTPS**：在生产中强制执行
- [ ] **Security Headers**: CSP, X-Frame-Options configured
- [ ] **安全 Headers**：CSP、 X-Frame-Options 已配置
- [ ] **Error Handling**: No sensitive data in errors
- [ ] **错误处理**：错误中无敏感数据
- [ ] **Logging**: No sensitive data logged
- [ ] **日志记录**：不记录敏感数据
- [ ] **Dependencies**: Up to date, no vulnerabilities
- [ ] **依赖**：最新，无漏洞
- [ ] **Row Level Security**: Enabled in Supabase
- [ ] **行级安全**：在 Supabase 中启用
- [ ] **CORS**: Properly configured
- [ ] **CORS**：正确配置
- [ ] **File Uploads**: Validated (size, type)
- [ ] **文件上传**：已验证（大小、类型）
- [ ] **Wallet Signatures**: Verified (if blockchain)
- [ ] **钱包签名**：已验证（如果是区块链）

## Resources
## 资源

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [Next.js Security](https://nextjs.org/docs/security)
- [Supabase Security](https://supabase.com/docs/guides/auth)
- [Web Security Academy](https://portswigger.net/web-security)

---

**Remember**: Security is not optional. One vulnerability can compromise the entire platform. When in doubt, err on the side of caution.
**记住**：安全不是可选的。一个漏洞可能会危及整个平台。如有疑问，请谨慎行事。
