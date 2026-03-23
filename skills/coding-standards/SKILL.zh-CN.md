---
name: coding-standards
description: Universal coding standards, best practices, and patterns for TypeScript, JavaScript, React, and Node.js development.
description zh-CN: TypeScript、JavaScript、React 和 Node.js 开发的通用编码标准、最佳实践和模式。
origin: ECC
---

# Coding Standards & Best Practices
# 编码标准与最佳实践

Universal coding standards applicable across all projects.
适用于所有项目的通用编码标准。

## When to Activate
## 何时激活

- Starting a new project or module
  - 启动新项目或模块
- Reviewing code for quality and maintainability
  - 审查代码质量和可维护性
- Refactoring existing code to follow conventions
  - 重构现有代码以遵循约定
- Enforcing naming, formatting, or structural consistency
  - 强制命名、格式或结构一致性
- Setting up linting, formatting, or type-checking rules
  - 设置 linting、格式化或类型检查规则
- Onboarding new contributors to coding conventions
  - 让新贡献者了解编码约定

## Code Quality Principles
## 代码质量原则

### 1. Readability First
### 1. 可读性优先
- Code is read more than written
  - 代码阅读次数多于编写次数
- Clear variable and function names
  - 清晰的变量和函数名
- Self-documenting code preferred over comments
  - 优先使用自文档化代码而非注释
- Consistent formatting
  - 一致的格式

### 2. KISS (Keep It Simple, Stupid)
### 2. KISS（保持简单，笨蛋）
- Simplest solution that works
  - 最简单的可行方案
- Avoid over-engineering
  - 避免过度设计
- No premature optimization
  - 不做过早优化
- Easy to understand > clever code
  - 易理解 > 聪明的代码

### 3. DRY (Don't Repeat Yourself)
### 3. DRY（不要重复自己）
- Extract common logic into functions
  - 将通用逻辑提取为函数
- Create reusable components
  - 创建可复用组件
- Share utilities across modules
  - 在模块间共享工具函数
- Avoid copy-paste programming
  - 避免复制粘贴编程

### 4. YAGNI (You Aren't Gonna Need It)
### 4. YAGNI（你不需要它）
- Don't build features before they're needed
  - 不要在需要之前构建功能
- Avoid speculative generality
  - 避免投机性概括
- Add complexity only when required
  - 仅在需要时添加复杂性
- Start simple, refactor when needed
  - 从简单开始，需要时重构

## TypeScript/JavaScript Standards
## TypeScript/JavaScript 标准

### Variable Naming
### 变量命名

```typescript
// ✅ GOOD: Descriptive names
// ✅ 好：描述性名称
const marketSearchQuery = 'election'
const isUserAuthenticated = true
const totalRevenue = 1000

// ❌ BAD: Unclear names
// ❌ 差：不清晰的名称
const q = 'election'
const flag = true
const x = 1000
```

### Function Naming
### 函数命名

```typescript
// ✅ GOOD: Verb-noun pattern
// ✅ 好：动词-名词模式
async function fetchMarketData(marketId: string) { }
function calculateSimilarity(a: number[], b: number[]) { }
function isValidEmail(email: string): boolean { }

// ❌ BAD: Unclear or noun-only
// ❌ 差：不清晰或仅名词
async function market(id: string) { }
function similarity(a, b) { }
function email(e) { }
```

### Immutability Pattern (CRITICAL)
### 不可变性模式（关键）

```typescript
// ✅ ALWAYS use spread operator
// ✅ 始终使用展开运算符
const updatedUser = {
  ...user,
  name: 'New Name'
}

const updatedArray = [...items, newItem]

// ❌ NEVER mutate directly
// ❌ 切勿直接修改
user.name = 'New Name'  // BAD
items.push(newItem)     // BAD
```

### Error Handling
### 错误处理

```typescript
// ✅ GOOD: Comprehensive error handling
// ✅ 好：全面错误处理
async function fetchData(url: string) {
  try {
    const response = await fetch(url)

    if (!response.ok) {
      throw new Error(`HTTP ${response.status}: ${response.statusText}`)
    }

    return await response.json()
  } catch (error) {
    console.error('Fetch failed:', error)
    throw new Error('Failed to fetch data')
  }
}

// ❌ BAD: No error handling
// ❌ 差：无错误处理
async function fetchData(url) {
  const response = await fetch(url)
  return response.json()
}
```

### Async/Await Best Practices
### Async/Await 最佳实践

```typescript
// ✅ GOOD: Parallel execution when possible
// ✅ 好：尽可能并行执行
const [users, markets, stats] = await Promise.all([
  fetchUsers(),
  fetchMarkets(),
  fetchStats()
])

// ❌ BAD: Sequential when unnecessary
// ❌ 差：不必要时顺序执行
const users = await fetchUsers()
const markets = await fetchMarkets()
const stats = await fetchStats()
```

### Type Safety
### 类型安全

```typescript
// ✅ GOOD: Proper types
// ✅ 好：正确的类型
interface Market {
  id: string
  name: string
  status: 'active' | 'resolved' | 'closed'
  created_at: Date
}

function getMarket(id: string): Promise<Market> {
  // Implementation
}

// ❌ BAD: Using 'any'
// ❌ 差：使用 'any'
function getMarket(id: any): Promise<any> {
  // Implementation
}
```

## React Best Practices
## React 最佳实践

### Component Structure
### 组件结构

```typescript
// ✅ GOOD: Functional component with types
// ✅ 好：带类型的函数组件
interface ButtonProps {
  children: React.ReactNode
  onClick: () => void
  disabled?: boolean
  variant?: 'primary' | 'secondary'
}

export function Button({
  children,
  onClick,
  disabled = false,
  variant = 'primary'
}: ButtonProps) {
  return (
    <button
      onClick={onClick}
      disabled={disabled}
      className={`btn btn-${variant}`}
    >
      {children}
    </button>
  )
}

// ❌ BAD: No types, unclear structure
// ❌ 差：无类型、结构不清晰
export function Button(props) {
  return <button onClick={props.onClick}>{props.children}</button>
}
```

### Custom Hooks
### 自定义 Hooks

```typescript
// ✅ GOOD: Reusable custom hook
// ✅ 好：可复用的自定义 hook
export function useDebounce<T>(value: T, delay: number): T {
  const [debouncedValue, setDebouncedValue] = useState<T>(value)

  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value)
    }, delay)

    return () => clearTimeout(handler)
  }, [value, delay])

  return debouncedValue
}

// Usage
// 使用
const debouncedQuery = useDebounce(searchQuery, 500)
```

### State Management
### 状态管理

```typescript
// ✅ GOOD: Proper state updates
// ✅ 好：正确的状态更新
const [count, setCount] = useState(0)

// Functional update for state based on previous state
// 基于前一状态的函数式更新
setCount(prev => prev + 1)

// ❌ BAD: Direct state reference
// ❌ 差：直接引用状态
setCount(count + 1)  // Can be stale in async scenarios
// 在异步场景中可能过时
```

### Conditional Rendering
### 条件渲染

```typescript
// ✅ GOOD: Clear conditional rendering
// ✅ 好：清晰的条件渲染
{isLoading && <Spinner />}
{error && <ErrorMessage error={error} />}
{data && <DataDisplay data={data} />}

// ❌ BAD: Ternary hell
// ❌ 差：三元地狱
{isLoading ? <Spinner /> : error ? <ErrorMessage error={error} /> : data ? <DataDisplay data={data} /> : null}
```

## API Design Standards
## API 设计标准

### REST API Conventions
### REST API 约定

```
GET    /api/markets              # List all markets
GET    /api/markets              # 列出所有市场
GET    /api/markets/:id          # Get specific market
GET    /api/markets/:id          # 获取特定市场
POST   /api/markets              # Create new market
POST   /api/markets              # 创建新市场
PUT    /api/markets/:id          # Update market (full)
PUT    /api/markets/:id          # 更新市场（完整）
PATCH  /api/markets/:id          # Update market (partial)
PATCH  /api/markets/:id          # 更新市场（部分）
DELETE /api/markets/:id          # Delete market
DELETE /api/markets/:id          # 删除市场

# Query parameters for filtering
# 用于过滤的查询参数
GET /api/markets?status=active&limit=10&offset=0
```

### Response Format
### 响应格式

```typescript
// ✅ GOOD: Consistent response structure
// ✅ 好：一致的响应结构
interface ApiResponse<T> {
  success: boolean
  data?: T
  error?: string
  meta?: {
    total: number
    page: number
    limit: number
  }
}

// Success response
// 成功响应
return NextResponse.json({
  success: true,
  data: markets,
  meta: { total: 100, page: 1, limit: 10 }
})

// Error response
// 错误响应
return NextResponse.json({
  success: false,
  error: 'Invalid request'
}, { status: 400 })
```

### Input Validation
### 输入验证

```typescript
import { z } from 'zod'

// ✅ GOOD: Schema validation
// ✅ 好：模式验证
const CreateMarketSchema = z.object({
  name: z.string().min(1).max(200),
  description: z.string().min(1).max(2000),
  endDate: z.string().datetime(),
  categories: z.array(z.string()).min(1)
})

export async function POST(request: Request) {
  const body = await request.json()

  try {
    const validated = CreateMarketSchema.parse(body)
    // Proceed with validated data
    // 继续处理验证后的数据
  } catch (error) {
    if (error instanceof z.ZodError) {
      return NextResponse.json({
        success: false,
        error: 'Validation failed',
        details: error.errors
      }, { status: 400 })
    }
  }
}
```

## File Organization
## 文件组织

### Project Structure
### 项目结构

```
src/
├── app/                    # Next.js App Router
│   ├── api/               # API routes
│   ├── markets/           # Market pages
│   └── (auth)/           # Auth pages (route groups)
├── components/            # React components
│   ├── ui/               # Generic UI components
│   ├── forms/            # Form components
│   └── layouts/          # Layout components
├── hooks/                # Custom React hooks
├── lib/                  # Utilities and configs
│   ├── api/             # API clients
│   ├── utils/           # Helper functions
│   └── constants/       # Constants
├── types/                # TypeScript types
└── styles/              # Global styles
```

### File Naming
### 文件命名

```
components/Button.tsx          # PascalCase for components
components/Button.tsx          # 组件使用 PascalCase
hooks/useAuth.ts              # camelCase with 'use' prefix
hooks/useAuth.ts              # 工具使用 camelCase 带 'use' 前缀
lib/formatDate.ts             # camelCase for utilities
lib/formatDate.ts             # 工具函数使用 camelCase
types/market.types.ts         # camelCase with .types suffix
types/market.types.ts         # 类型使用 camelCase 带 .types 后缀
```

## Comments & Documentation
## 注释与文档

### When to Comment
### 何时注释

```typescript
// ✅ GOOD: Explain WHY, not WHAT
// ✅ 好：解释为什么，不是做什么
// Use exponential backoff to avoid overwhelming the API during outages
// 使用指数退避以避免在故障期间压垮 API
const delay = Math.min(1000 * Math.pow(2, retryCount), 30000)

// Deliberately using mutation here for performance with large arrays
// 在此有意使用修改以提升大数组性能
items.push(newItem)

// ❌ BAD: Stating the obvious
// ❌ 差：陈述显而易见的事
// Increment counter by 1
// 计数器加 1
count++

// Set name to user's name
// 将名称设置为用户名
name = user.name
```

### JSDoc for Public APIs
### 公共 API 的 JSDoc

```typescript
/**
 * Searches markets using semantic similarity.
 *
 * @param query - Natural language search query
 * @param limit - Maximum number of results (default: 10)
 * @returns Array of markets sorted by similarity score
 * @throws {Error} If OpenAI API fails or Redis unavailable
 *
 * @example
 * ```typescript
 * const results = await searchMarkets('election', 5)
 * console.log(results[0].name) // "Trump vs Biden"
 * ```
 */
export async function searchMarkets(
  query: string,
  limit: number = 10
): Promise<Market[]> {
  // Implementation
}
```

## Performance Best Practices
## 性能最佳实践

### Memoization
### 记忆化

```typescript
import { useMemo, useCallback } from 'react'

// ✅ GOOD: Memoize expensive computations
// ✅ 好：记忆化昂贵计算
const sortedMarkets = useMemo(() => {
  return markets.sort((a, b) => b.volume - a.volume)
}, [markets])

// ✅ GOOD: Memoize callbacks
// ✅ 好：记忆化回调
const handleSearch = useCallback((query: string) => {
  setSearchQuery(query)
}, [])
```

### Lazy Loading
### 懒加载

```typescript
import { lazy, Suspense } from 'react'

// ✅ GOOD: Lazy load heavy components
// ✅ 好：懒加载重型组件
const HeavyChart = lazy(() => import('./HeavyChart'))

export function Dashboard() {
  return (
    <Suspense fallback={<Spinner />}>
      <HeavyChart />
    </Suspense>
  )
}
```

### Database Queries
### 数据库查询

```typescript
// ✅ GOOD: Select only needed columns
// ✅ 好：仅选择需要的列
const { data } = await supabase
  .from('markets')
  .select('id, name, status')
  .limit(10)

// ❌ BAD: Select everything
// ❌ 差：选择所有
const { data } = await supabase
  .from('markets')
  .select('*')
```

## Testing Standards
## 测试标准

### Test Structure (AAA Pattern)
### 测试结构（AAA 模式）

```typescript
test('calculates similarity correctly', () => {
  // Arrange
  const vector1 = [1, 0, 0]
  const vector2 = [0, 1, 0]

  // Act
  const similarity = calculateCosineSimilarity(vector1, vector2)

  // Assert
  expect(similarity).toBe(0)
})
```

### Test Naming
### 测试命名

```typescript
// ✅ GOOD: Descriptive test names
// ✅ 好：描述性测试名称
test('returns empty array when no markets match query', () => { })
test('throws error when OpenAI API key is missing', () => { })
test('falls back to substring search when Redis unavailable', () => { })

// ❌ BAD: Vague test names
// ❌ 差：模糊测试名称
test('works', () => { })
test('test search', () => { })
```

## Code Smell Detection
## 代码异味检测

Watch for these anti-patterns:
注意这些反模式：

### 1. Long Functions
### 1. 过长函数

```typescript
// ❌ BAD: Function > 50 lines
// ❌ 差：函数 > 50 行
function processMarketData() {
  // 100 lines of code
}

// ✅ GOOD: Split into smaller functions
// ✅ 好：拆分为更小的函数
function processMarketData() {
  const validated = validateData()
  const transformed = transformData(validated)
  return saveData(transformed)
}
```

### 2. Deep Nesting
### 2. 过深嵌套

```typescript
// ❌ BAD: 5+ levels of nesting
// ❌ 差：5层以上嵌套
if (user) {
  if (user.isAdmin) {
    if (market) {
      if (market.isActive) {
        if (hasPermission) {
          // Do something
        }
      }
    }
  }
}

// ✅ GOOD: Early returns
// ✅ 好：提前返回
if (!user) return
if (!user.isAdmin) return
if (!market) return
if (!market.isActive) return
if (!hasPermission) return

// Do something
```

### 3. Magic Numbers
### 3. 魔法数字

```typescript
// ❌ BAD: Unexplained numbers
// ❌ 差：未解释的数字
if (retryCount > 3) { }
setTimeout(callback, 500)

// ✅ GOOD: Named constants
// ✅ 好：命名常量
const MAX_RETRIES = 3
const DEBOUNCE_DELAY_MS = 500

if (retryCount > MAX_RETRIES) { }
setTimeout(callback, DEBOUNCE_DELAY_MS)
```

**Remember**: Code quality is not negotiable. Clear, maintainable code enables rapid development and confident refactoring.
**记住**：代码质量不容商量。清晰、可维护的代码支持快速开发和自信的重构。
