---
name: backend-patterns
description: Backend architecture patterns, API design, database optimization, and server-side best practices for Node.js, Express, and Next.js API routes.
description zh-CN: 后端架构模式、API 设计、数据库优化，以及 Node.js、Express 和 Next.js API 路由的服务器端最佳实践。
origin: ECC
---

# Backend Development Patterns

# Backend Development Patterns
# 后端开发模式

Backend architecture patterns and best practices for scalable server-side applications.
可扩展服务器端应用的后端架构模式和最佳实践。

## When to Activate

## When to Activate
## 何时激活

- Designing REST or GraphQL API endpoints
  - 设计 REST 或 GraphQL API 端点
- Implementing repository, service, or controller layers
  - 实现 repository、service 或 controller 层
- Optimizing database queries (N+1, indexing, connection pooling)
  - 优化数据库查询（N+1、索引、连接池）
- Adding caching (Redis, in-memory, HTTP cache headers)
  - 添加缓存（Redis、内存、HTTP 缓存头）
- Setting up background jobs or async processing
  - 设置后台作业或异步处理
- Structuring error handling and validation for APIs
  - 为 API 构建错误处理和验证结构
- Building middleware (auth, logging, rate limiting)
  - 构建中间件（认证、日志、限流）

## API Design Patterns

## API Design Patterns
## API 设计模式

### RESTful API Structure

### RESTful API Structure
### RESTful API 结构

```typescript
// ✅ Resource-based URLs
// ✅ 基于资源的 URL
GET    /api/markets                 # List resources
GET    /api/markets                 # 列出资源
GET    /api/markets/:id             # Get single resource
GET    /api/markets/:id             # 获取单个资源
POST   /api/markets                 # Create resource
POST   /api/markets                 # 创建资源
PUT    /api/markets/:id             # Replace resource
PUT    /api/markets/:id             # 替换资源
PATCH  /api/markets/:id             # Update resource
PATCH  /api/markets/:id             # 更新资源
DELETE /api/markets/:id             # Delete resource
DELETE /api/markets/:id             # 删除资源

// ✅ Query parameters for filtering, sorting, pagination
// ✅ 查询参数用于过滤、排序、分页
GET /api/markets?status=active&sort=volume&limit=20&offset=0
```

### Repository Pattern

### Repository Pattern
### Repository 模式

```typescript
// Abstract data access logic
// 抽象数据访问逻辑
interface MarketRepository {
  findAll(filters?: MarketFilters): Promise<Market[]>
  findById(id: string): Promise<Market | null>
  create(data: CreateMarketDto): Promise<Market>
  update(id: string, data: UpdateMarketDto): Promise<Market>
  delete(id: string): Promise<void>
}

class SupabaseMarketRepository implements MarketRepository {
  async findAll(filters?: MarketFilters): Promise<Market[]> {
    let query = supabase.from('markets').select('*')

    if (filters?.status) {
      query = query.eq('status', filters.status)
    }

    if (filters?.limit) {
      query = query.limit(filters.limit)
    }

    const { data, error } = await query

    if (error) throw new Error(error.message)
    return data
  }

  // Other methods...
  // 其他方法...
}
```

### Service Layer Pattern

### Service Layer Pattern
### Service 层模式

```typescript
// Business logic separated from data access
// 业务逻辑与数据访问分离
class MarketService {
  constructor(private marketRepo: MarketRepository) {}

  async searchMarkets(query: string, limit: number = 10): Promise<Market[]> {
    // Business logic
    // 业务逻辑
    const embedding = await generateEmbedding(query)
    const results = await this.vectorSearch(embedding, limit)

    // Fetch full data
    // 获取完整数据
    const markets = await this.marketRepo.findByIds(results.map(r => r.id))

    // Sort by similarity
    // 按相似度排序
    return markets.sort((a, b) => {
      const scoreA = results.find(r => r.id === a.id)?.score || 0
      const scoreB = results.find(r => r.id === b.id)?.score || 0
      return scoreA - scoreB
    })
  }

  private async vectorSearch(embedding: number[], limit: number) {
    // Vector search implementation
    // 向量搜索实现
  }
}
```

### Middleware Pattern

### Middleware Pattern
### 中间件模式

```typescript
// Request/response processing pipeline
// 请求/响应处理管道
export function withAuth(handler: NextApiHandler): NextApiHandler {
  return async (req, res) => {
    const token = req.headers.authorization?.replace('Bearer ', '')

    if (!token) {
      return res.status(401).json({ error: 'Unauthorized' })
    }

    try {
      const user = await verifyToken(token)
      req.user = user
      return handler(req, res)
    } catch (error) {
      return res.status(401).json({ error: 'Invalid token' })
    }
  }
}

// Usage
// 使用
export default withAuth(async (req, res) => {
  // Handler has access to req.user
  // 处理函数可以访问 req.user
})
```

## Database Patterns

## Database Patterns
## 数据库模式

### Query Optimization

### Query Optimization
### 查询优化

```typescript
// ✅ GOOD: Select only needed columns
// ✅ 好：只选择需要的列
const { data } = await supabase
  .from('markets')
  .select('id, name, status, volume')
  .eq('status', 'active')
  .order('volume', { ascending: false })
  .limit(10)

// ❌ BAD: Select everything
// ❌ 差：选择所有列
const { data } = await supabase
  .from('markets')
  .select('*')
```

### N+1 Query Prevention

### N+1 Query Prevention
### N+1 查询预防

```typescript
// ❌ BAD: N+1 query problem
// ❌ 差：N+1 查询问题
const markets = await getMarkets()
for (const market of markets) {
  market.creator = await getUser(market.creator_id)  // N queries
  // N 个查询
}

// ✅ GOOD: Batch fetch
// ✅ 好：批量获取
const markets = await getMarkets()
const creatorIds = markets.map(m => m.creator_id)
const creators = await getUsers(creatorIds)  // 1 query
// 1 个查询
const creatorMap = new Map(creators.map(c => [c.id, c]))

markets.forEach(market => {
  market.creator = creatorMap.get(market.creator_id)
})
```

### Transaction Pattern

### Transaction Pattern
### 事务模式

```typescript
async function createMarketWithPosition(
  marketData: CreateMarketDto,
  positionData: CreatePositionDto
) {
  // Use Supabase transaction
  // 使用 Supabase 事务
  const { data, error } = await supabase.rpc('create_market_with_position', {
    market_data: marketData,
    position_data: positionData
  })

  if (error) throw new Error('Transaction failed')
  return data
}

// SQL function in Supabase
// Supabase 中的 SQL 函数
CREATE OR REPLACE FUNCTION create_market_with_position(
  market_data jsonb,
  position_data jsonb
)
RETURNS jsonb
LANGUAGE plpgsql
AS $$
BEGIN
  -- Start transaction automatically
  // 自动开始事务
  INSERT INTO markets VALUES (market_data);
  INSERT INTO positions VALUES (position_data);
  RETURN jsonb_build_object('success', true);
EXCEPTION
  WHEN OTHERS THEN
    -- Rollback happens automatically
    // 自动回滚
    RETURN jsonb_build_object('success', false, 'error', SQLERRM);
END;
$$;
```

## Caching Strategies

## Caching Strategies
## 缓存策略

### Redis Caching Layer

### Redis Caching Layer
### Redis 缓存层

```typescript
class CachedMarketRepository implements MarketRepository {
  constructor(
    private baseRepo: MarketRepository,
    private redis: RedisClient
  ) {}

  async findById(id: string): Promise<Market | null> {
    // Check cache first
    // 首先检查缓存
    const cached = await this.redis.get(`market:${id}`)

    if (cached) {
      return JSON.parse(cached)
    }

    // Cache miss - fetch from database
    // 缓存未命中——从数据库获取
    const market = await this.baseRepo.findById(id)

    if (market) {
      // Cache for 5 minutes
      // 缓存 5 分钟
      await this.redis.setex(`market:${id}`, 300, JSON.stringify(market))
    }

    return market
  }

  async invalidateCache(id: string): Promise<void> {
    await this.redis.del(`market:${id}`)
  }
}
```

### Cache-Aside Pattern

### Cache-Aside Pattern
### Cache-Aside 模式

```typescript
async function getMarketWithCache(id: string): Promise<Market> {
  const cacheKey = `market:${id}`

  // Try cache
  // 尝试缓存
  const cached = await redis.get(cacheKey)
  if (cached) return JSON.parse(cached)

  // Cache miss - fetch from DB
  // 缓存未命中——从 DB 获取
  const market = await db.markets.findUnique({ where: { id } })

  if (!market) throw new Error('Market not found')

  // Update cache
  // 更新缓存
  await redis.setex(cacheKey, 300, JSON.stringify(market))

  return market
}
```

## Error Handling Patterns

## Error Handling Patterns
## 错误处理模式

### Centralized Error Handler

### Centralized Error Handler
### 集中式错误处理器

```typescript
class ApiError extends Error {
  constructor(
    public statusCode: number,
    public message: string,
    public isOperational = true
  ) {
    super(message)
    Object.setPrototypeOf(this, ApiError.prototype)
  }
}

export function errorHandler(error: unknown, req: Request): Response {
  if (error instanceof ApiError) {
    return NextResponse.json({
      success: false,
      error: error.message
    }, { status: error.statusCode })
  }

  if (error instanceof z.ZodError) {
    return NextResponse.json({
      success: false,
      error: 'Validation failed',
      details: error.errors
    }, { status: 400 })
  }

  // Log unexpected errors
  // 记录意外错误
  console.error('Unexpected error:', error)

  return NextResponse.json({
    success: false,
    error: 'Internal server error'
  }, { status: 500 })
}

// Usage
// 使用
export async function GET(request: Request) {
  try {
    const data = await fetchData()
    return NextResponse.json({ success: true, data })
  } catch (error) {
    return errorHandler(error, request)
  }
}
```

### Retry with Exponential Backoff

### Retry with Exponential Backoff
### 指数退避重试

```typescript
async function fetchWithRetry<T>(
  fn: () => Promise<T>,
  maxRetries = 3
): Promise<T> {
  let lastError: Error

  for (let i = 0; i < maxRetries; i++) {
    try {
      return await fn()
    } catch (error) {
      lastError = error as Error

      if (i < maxRetries - 1) {
        // Exponential backoff: 1s, 2s, 4s
        // 指数退避：1s、2s、4s
        const delay = Math.pow(2, i) * 1000
        await new Promise(resolve => setTimeout(resolve, delay))
      }
    }
  }

  throw lastError!
}

// Usage
// 使用
const data = await fetchWithRetry(() => fetchFromAPI())
```

## Authentication & Authorization

## Authentication & Authorization
## 认证和授权

### JWT Token Validation

### JWT Token Validation
### JWT 令牌验证

```typescript
import jwt from 'jsonwebtoken'

interface JWTPayload {
  userId: string
  email: string
  role: 'admin' | 'user'
}

export function verifyToken(token: string): JWTPayload {
  try {
    const payload = jwt.verify(token, process.env.JWT_SECRET!) as JWTPayload
    return payload
  } catch (error) {
    throw new ApiError(401, 'Invalid token')
  }
}

export async function requireAuth(request: Request) {
  const token = request.headers.get('authorization')?.replace('Bearer ', '')

  if (!token) {
    throw new ApiError(401, 'Missing authorization token')
  }

  return verifyToken(token)
}

// Usage in API route
// 在 API 路由中使用
export async function GET(request: Request) {
  const user = await requireAuth(request)

  const data = await getDataForUser(user.userId)

  return NextResponse.json({ success: true, data })
}
```

### Role-Based Access Control

### Role-Based Access Control
### 基于角色的访问控制

```typescript
type Permission = 'read' | 'write' | 'delete' | 'admin'

interface User {
  id: string
  role: 'admin' | 'moderator' | 'user'
}

const rolePermissions: Record<User['role'], Permission[]> = {
  admin: ['read', 'write', 'delete', 'admin'],
  moderator: ['read', 'write', 'delete'],
  user: ['read', 'write']
}

export function hasPermission(user: User, permission: Permission): boolean {
  return rolePermissions[user.role].includes(permission)
}

export function requirePermission(permission: Permission) {
  return (handler: (request: Request, user: User) => Promise<Response>) => {
    return async (request: Request) => {
      const user = await requireAuth(request)

      if (!hasPermission(user, permission)) {
        throw new ApiError(403, 'Insufficient permissions')
      }

      return handler(request, user)
    }
  }
}

// Usage - HOF wraps the handler
// 使用——高阶函数包装处理函数
export const DELETE = requirePermission('delete')(
  async (request: Request, user: User) => {
    // Handler receives authenticated user with verified permission
    // 处理函数接收带有已验证权限的已认证用户
    return new Response('Deleted', { status: 200 })
  }
)
```

## Rate Limiting

## Rate Limiting
## 限流

### Simple In-Memory Rate Limiter

### Simple In-Memory Rate Limiter
### 简单内存限流器

```typescript
class RateLimiter {
  private requests = new Map<string, number[]>()

  async checkLimit(
    identifier: string,
    maxRequests: number,
    windowMs: number
  ): Promise<boolean> {
    const now = Date.now()
    const requests = this.requests.get(identifier) || []

    // Remove old requests outside window
    // 移除窗口外的旧请求
    const recentRequests = requests.filter(time => now - time < windowMs)

    if (recentRequests.length >= maxRequests) {
      return false  // Rate limit exceeded
      // 超过限流
    }

    // Add current request
    // 添加当前请求
    recentRequests.push(now)
    this.requests.set(identifier, recentRequests)

    return true
  }
}

const limiter = new RateLimiter()

export async function GET(request: Request) {
  const ip = request.headers.get('x-forwarded-for') || 'unknown'

  const allowed = await limiter.checkLimit(ip, 100, 60000)  // 100 req/min
  // 100 请求/分钟

  if (!allowed) {
    return NextResponse.json({
      error: 'Rate limit exceeded'
    }, { status: 429 })
  }

  // Continue with request
  // 继续处理请求
}
```

## Background Jobs & Queues

## Background Jobs & Queues
## 后台作业和队列

### Simple Queue Pattern

### Simple Queue Pattern
### 简单队列模式

```typescript
class JobQueue<T> {
  private queue: T[] = []
  private processing = false

  async add(job: T): Promise<void> {
    this.queue.push(job)

    if (!this.processing) {
      this.process()
    }
  }

  private async process(): Promise<void> {
    this.processing = true

    while (this.queue.length > 0) {
      const job = this.queue.shift()!

      try {
        await this.execute(job)
      } catch (error) {
        console.error('Job failed:', error)
      }
    }

    this.processing = false
  }

  private async execute(job: T): Promise<void> {
    // Job execution logic
    // 作业执行逻辑
  }
}

// Usage for indexing markets
// 用于索引市场的使用
interface IndexJob {
  marketId: string
}

const indexQueue = new JobQueue<IndexJob>()

export async function POST(request: Request) {
  const { marketId } = await request.json()

  // Add to queue instead of blocking
  // 添加到队列而非阻塞
  await indexQueue.add({ marketId })

  return NextResponse.json({ success: true, message: 'Job queued' })
}
```

## Logging & Monitoring

## Logging & Monitoring
## 日志和监控

### Structured Logging

### Structured Logging
### 结构化日志

```typescript
interface LogContext {
  userId?: string
  requestId?: string
  method?: string
  path?: string
  [key: string]: unknown
}

class Logger {
  log(level: 'info' | 'warn' | 'error', message: string, context?: LogContext) {
    const entry = {
      timestamp: new Date().toISOString(),
      level,
      message,
      ...context
    }

    console.log(JSON.stringify(entry))
  }

  info(message: string, context?: LogContext) {
    this.log('info', message, context)
  }

  warn(message: string, context?: LogContext) {
    this.log('warn', message, context)
  }

  error(message: string, error: Error, context?: LogContext) {
    this.log('error', message, {
      ...context,
      error: error.message,
      stack: error.stack
    })
  }
}

const logger = new Logger()

// Usage
// 使用
export async function GET(request: Request) {
  const requestId = crypto.randomUUID()

  logger.info('Fetching markets', {
    requestId,
    method: 'GET',
    path: '/api/markets'
  })

  try {
    const markets = await fetchMarkets()
    return NextResponse.json({ success: true, data: markets })
  } catch (error) {
    logger.error('Failed to fetch markets', error as Error, { requestId })
    return NextResponse.json({ error: 'Internal error' }, { status: 500 })
  }
}
```

**Remember**: Backend patterns enable scalable, maintainable server-side applications. Choose patterns that fit your complexity level.
**记住**：后端模式使可扩展、可维护的服务器端应用成为可能。选择适合你复杂度级别的模式。
