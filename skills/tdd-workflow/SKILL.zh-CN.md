---
name: tdd-workflow
description: Use this skill when writing new features, fixing bugs, or refactoring code. Enforces test-driven development with 80%+ coverage including unit, integration, and E2E tests.
description zh-CN: 编写新功能、修复bug或重构代码时使用此skill。强制测试驱动开发，要求80%以上覆盖率，包括单元测试、集成测试和E2E测试。
origin: ECC
---

# Test-Driven Development Workflow

## English

This skill ensures all code development follows TDD principles with comprehensive test coverage.

## 中文

此skill确保所有代码开发遵循TDD原则，并具有全面的测试覆盖率。

## When to Activate

## English

- Writing new features or functionality
- Fixing bugs or issues
- Refactoring existing code
- Adding API endpoints
- Creating new components

## 中文

- 编写新功能或功能
- 修复bug或问题
- 重构现有代码
- 添加API端点
- 创建新组件

## Core Principles

## English

### 1. Tests BEFORE Code
ALWAYS write tests first, then implement code to make tests pass.

### 2. Coverage Requirements
- Minimum 80% coverage (unit + integration + E2E)
- All edge cases covered
- Error scenarios tested
- Boundary conditions verified

### 3. Test Types

#### Unit Tests
- Individual functions and utilities
- Component logic
- Pure functions
- Helpers and utilities

#### Integration Tests
- API endpoints
- Database operations
- Service interactions
- External API calls

#### E2E Tests (Playwright)
- Critical user flows
- Complete workflows
- Browser automation
- UI interactions

## 中文

### 核心原则

#### 1. 测试优先于代码
始终先写测试，然后实现代码使测试通过。

#### 2. 覆盖率要求
- 最少80%覆盖率（单元+集成+E2E）
- 覆盖所有边界情况
- 测试错误场景
- 验证边界条件

#### 3. 测试类型

##### 单元测试
- 单独函数和工具函数
- 组件逻辑
- 纯函数
- 辅助函数和工具

##### 集成测试
- API端点
- 数据库操作
- 服务交互
- 外部API调用

##### E2E测试（Playwright）
- 关键用户流程
- 完整工作流
- 浏览器自动化
- UI交互

## TDD Workflow Steps

## English

### Step 1: Write User Journeys

## 中文

### TDD工作流步骤

#### 步骤1：编写用户旅程

```
As a [role], I want to [action], so that [benefit]

Example:
As a user, I want to search for markets semantically,
so that I can find relevant markets even without exact keywords.

作为[角色]，我想要[动作]，以便[收益]

示例：
作为用户，我希望语义化搜索市场，
以便即使没有精确关键词也能找到相关市场。
```

### Step 2: Generate Test Cases

## English

For each user journey, create comprehensive test cases:

## 中文

#### 步骤2：生成测试用例

为每个用户旅程创建全面的测试用例：

```typescript
describe('Semantic Search', () => {
  it('returns relevant markets for query', async () => {
    // Test implementation
    // 测试实现
  })

  it('handles empty query gracefully', async () => {
    // Test edge case
    // 测试边界情况
  })

  it('falls back to substring search when Redis unavailable', async () => {
    // Test fallback behavior
    // 测试回退行为
  })

  it('sorts results by similarity score', async () => {
    // Test sorting logic
    // 测试排序逻辑
  })
})
```

### Step 3: Run Tests (They Should Fail)

## English

## 中文

#### 步骤3：运行测试（应该失败）

```bash
npm test
# Tests should fail - we haven't implemented yet
# 测试应该失败——我们还没有实现
```

### Step 4: Implement Code

## English

Write minimal code to make tests pass:

## 中文

#### 步骤4：实现代码

编写最少的代码使测试通过：

```typescript
// Implementation guided by tests
// 测试引导的实现
export async function searchMarkets(query: string) {
  // Implementation here
  // 此处实现
}
```

### Step 5: Run Tests Again

## English

## 中文

#### 步骤5：再次运行测试

```bash
npm test
# Tests should now pass
# 测试现在应该通过
```

### Step 6: Refactor

## English

Improve code quality while keeping tests green:

## 中文

#### 步骤6：重构

在保持测试通过的同时改进代码质量：

- Remove duplication
  消除重复
- Improve naming
  改进命名
- Optimize performance
  优化性能
- Enhance readability
  增强可读性

### Step 7: Verify Coverage

## English

## 中文

#### 步骤7：验证覆盖率

```bash
npm run test:coverage
# Verify 80%+ coverage achieved
# 验证达到80%以上覆盖率
```

## Testing Patterns

## English

### Unit Test Pattern (Jest/Vitest)

## 中文

### 测试模式

#### 单元测试模式（Jest/Vitest）

```typescript
import { render, screen, fireEvent } from '@testing-library/react'
import { Button } from './Button'

describe('Button Component', () => {
  it('renders with correct text', () => {
    render(<Button>Click me</Button>)
    expect(screen.getByText('Click me')).toBeInTheDocument()
  })

  it('calls onClick when clicked', () => {
    const handleClick = jest.fn()
    render(<Button onClick={handleClick}>Click</Button>)

    fireEvent.click(screen.getByRole('button'))

    expect(handleClick).toHaveBeenCalledTimes(1)
  })

  it('is disabled when disabled prop is true', () => {
    render(<Button disabled>Click</Button>)
    expect(screen.getByRole('button')).toBeDisabled()
  })
})
```

### API Integration Test Pattern

## English

## 中文

#### API集成测试模式

```typescript
import { NextRequest } from 'next/server'
import { GET } from './route'

describe('GET /api/markets', () => {
  it('returns markets successfully', async () => {
    const request = new NextRequest('http://localhost/api/markets')
    const response = await GET(request)
    const data = await response.json()

    expect(response.status).toBe(200)
    expect(data.success).toBe(true)
    expect(Array.isArray(data.data)).toBe(true)
  })

  it('validates query parameters', async () => {
    const request = new NextRequest('http://localhost/api/markets?limit=invalid')
    const response = await GET(request)

    expect(response.status).toBe(400)
  })

  it('handles database errors gracefully', async () => {
    // Mock database failure
    // 模拟数据库故障
    const request = new NextRequest('http://localhost/api/markets')
    // Test error handling
    // 测试错误处理
  })
})
```

### E2E Test Pattern (Playwright)

## English

## 中文

#### E2E测试模式（Playwright）

```typescript
import { test, expect } from '@playwright/test'

test('user can search and filter markets', async ({ page }) => {
  // Navigate to markets page
  // 导航到市场页面
  await page.goto('/')
  await page.click('a[href="/markets"]')

  // Verify page loaded
  // 验证页面加载
  await expect(page.locator('h1')).toContainText('Markets')

  // Search for markets
  // 搜索市场
  await page.fill('input[placeholder="Search markets"]', 'election')

  // Wait for debounce and results
  // 等待防抖和结果
  await page.waitForTimeout(600)

  // Verify search results displayed
  // 验证搜索结果显示
  const results = page.locator('[data-testid="market-card"]')
  await expect(results).toHaveCount(5, { timeout: 5000 })

  // Verify results contain search term
  // 验证结果包含搜索词
  const firstResult = results.first()
  await expect(firstResult).toContainText('election', { ignoreCase: true })

  // Filter by status
  // 按状态筛选
  await page.click('button:has-text("Active")')

  // Verify filtered results
  // 验证筛选结果
  await expect(results).toHaveCount(3)
})

test('user can create a new market', async ({ page }) => {
  // Login first
  // 首先登录
  await page.goto('/creator-dashboard')

  // Fill market creation form
  // 填写市场创建表单
  await page.fill('input[name="name"]', 'Test Market')
  await page.fill('textarea[name="description"]', 'Test description')
  await page.fill('input[name="endDate"]', '2025-12-31')

  // Submit form
  // 提交表单
  await page.click('button[type="submit"]')

  // Verify success message
  // 验证成功消息
  await expect(page.locator('text=Market created successfully')).toBeVisible()

  // Verify redirect to market page
  // 验证重定向到市场页面
  await expect(page).toHaveURL(/\/markets\/test-market/)
})
```

## Test File Organization

## English

## 中文

### 测试文件组织

```
src/
├── components/
│   ├── Button/
│   │   ├── Button.tsx
│   │   ├── Button.test.tsx          # Unit tests 单元测试
│   │   └── Button.stories.tsx       # Storybook
│   └── MarketCard/
│       ├── MarketCard.tsx
│       └── MarketCard.test.tsx
├── app/
│   └── api/
│       └── markets/
│           ├── route.ts
│           └── route.test.ts         # Integration tests 集成测试
└── e2e/
    ├── markets.spec.ts               # E2E tests E2E测试
    ├── trading.spec.ts
    └── auth.spec.ts
```

## Mocking External Services

## English

### Supabase Mock

## 中文

### 模拟外部服务

#### Supabase模拟

```typescript
jest.mock('@/lib/supabase', () => ({
  supabase: {
    from: jest.fn(() => ({
      select: jest.fn(() => ({
        eq: jest.fn(() => Promise.resolve({
          data: [{ id: 1, name: 'Test Market' }],
          error: null
        }))
      }))
    }))
  }
}))
```

### Redis Mock

## English

## 中文

#### Redis模拟

```typescript
jest.mock('@/lib/redis', () => ({
  searchMarketsByVector: jest.fn(() => Promise.resolve([
    { slug: 'test-market', similarity_score: 0.95 }
  ])),
  checkRedisHealth: jest.fn(() => Promise.resolve({ connected: true }))
}))
```

### OpenAI Mock

## English

## 中文

#### OpenAI模拟

```typescript
jest.mock('@/lib/openai', () => ({
  generateEmbedding: jest.fn(() => Promise.resolve(
    new Array(1536).fill(0.1) // Mock 1536-dim embedding 模拟1536维嵌入
  ))
}))
```

## Test Coverage Verification

## English

### Run Coverage Report

## 中文

### 测试覆盖率验证

#### 运行覆盖率报告

```bash
npm run test:coverage
```

### Coverage Thresholds

## English

## 中文

#### 覆盖率阈值

```json
{
  "jest": {
    "coverageThresholds": {
      "global": {
        "branches": 80,
        "functions": 80,
        "lines": 80,
        "statements": 80
      }
    }
  }
}
```

## Common Testing Mistakes to Avoid

## English

### ❌ WRONG: Testing Implementation Details

## 中文

### 应避免的常见测试错误

#### ❌ 错误：测试实现细节

```typescript
// Don't test internal state
// 不要测试内部状态
expect(component.state.count).toBe(5)
```

### ✅ CORRECT: Test User-Visible Behavior

#### ✅ 正确：测试用户可见行为

```typescript
// Test what users see
// 测试用户看到的内容
expect(screen.getByText('Count: 5')).toBeInTheDocument()
```

### ❌ WRONG: Brittle Selectors

#### ❌ 错误：脆弱的选择器

```typescript
// Breaks easily
// 容易破坏
await page.click('.css-class-xyz')
```

### ✅ CORRECT: Semantic Selectors

#### ✅ 正确：语义化选择器

```typescript
// Resilient to changes
// 对变化有韧性
await page.click('button:has-text("Submit")')
await page.click('[data-testid="submit-button"]')
```

### ❌ WRONG: No Test Isolation

#### ❌ 错误：无测试隔离

```typescript
// Tests depend on each other
// 测试相互依赖
test('creates user', () => { /* ... */ })
test('updates same user', () => { /* depends on previous test */ })
```

### ✅ CORRECT: Independent Tests

#### ✅ 正确：独立测试

```typescript
// Each test sets up its own data
// 每个测试设置自己的数据
test('creates user', () => {
  const user = createTestUser()
  // Test logic
  // 测试逻辑
})

test('updates user', () => {
  const user = createTestUser()
  // Update logic
  // 更新逻辑
})
```

## Continuous Testing

## English

### Watch Mode During Development

## 中文

### 持续测试

#### 开发期间的监视模式

```bash
npm test -- --watch
# Tests run automatically on file changes
# 文件更改时自动运行测试
```

### Pre-Commit Hook

## 中文

#### 提交前钩子

```bash
# Runs before every commit
# 每次提交前运行
npm test && npm run lint
```

### CI/CD Integration

## English

## 中文

#### CI/CD集成

```yaml
# GitHub Actions
- name: Run Tests
  run: npm test -- --coverage
- name: Upload Coverage
  uses: codecov/codecov-action@v3
```

## Best Practices

## English

1. **Write Tests First** - Always TDD
2. **One Assert Per Test** - Focus on single behavior
3. **Descriptive Test Names** - Explain what's tested
4. **Arrange-Act-Assert** - Clear test structure
5. **Mock External Dependencies** - Isolate unit tests
6. **Test Edge Cases** - Null, undefined, empty, large
7. **Test Error Paths** - Not just happy paths
8. **Keep Tests Fast** - Unit tests < 50ms each
9. **Clean Up After Tests** - No side effects
10. **Review Coverage Reports** - Identify gaps

## 中文

### 最佳实践

1. **先写测试** - 始终TDD
2. **每个测试一个断言** - 专注于单一行为
3. **描述性测试名称** - 解释测试内容
4. **Arrange-Act-Assert** - 清晰的测试结构
5. **模拟外部依赖** - 隔离单元测试
6. **测试边界情况** - Null、undefined、空、大
7. **测试错误路径** - 不仅仅是成功路径
8. **保持测试快速** - 单元测试每个<50ms
9. **测试后清理** - 无副作用
10. **审查覆盖率报告** - 识别差距

## Success Metrics

## English

- 80%+ code coverage achieved
- All tests passing (green)
- No skipped or disabled tests
- Fast test execution (< 30s for unit tests)
- E2E tests cover critical user flows
- Tests catch bugs before production

## 中文

### 成功指标

- 达到80%以上代码覆盖率
- 所有测试通过（绿色）
- 无跳过或禁用的测试
- 快速测试执行（单元测试<30秒）
- E2E测试覆盖关键用户流程
- 测试在生产前捕获bug

---

**Remember**: Tests are not optional. They are the safety net that enables confident refactoring, rapid development, and production reliability.

**记住**：测试不是可选的。它们是使有信心的重构、快速开发和生产可靠性成为可能的安全网。
