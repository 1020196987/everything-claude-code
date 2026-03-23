---
name: ai-regression-testing
description: Regression testing strategies for AI-assisted development. Sandbox-mode API testing without database dependencies, automated bug-check workflows, and patterns to catch AI blind spots where the same model writes and reviews code.
description zh-CN: AI 辅助开发的回归测试策略。无数据库依赖的沙箱模式 API 测试、自动 bug 检查工作流，以及捕获 AI 盲点的模式——当同一模型编写和审查代码时会产生的盲点。
origin: ECC
---

# AI Regression Testing

# AI Regression Testing
# AI 回归测试

Testing patterns specifically designed for AI-assisted development, where the same model writes code and reviews it — creating systematic blind spots that only automated tests can catch.
专门为 AI 辅助开发设计的测试模式，当同一模型编写和审查代码时会创建系统性盲点，只有自动化测试才能捕获。

## When to Activate

## When to Activate
## 何时激活

- AI agent (Claude Code, Cursor, Codex) has modified API routes or backend logic
  - AI agent（Claude Code、Cursor、Codex）已修改 API 路由或后端逻辑
- A bug was found and fixed — need to prevent re-introduction
  - 发现并修复了 bug——需要防止重新引入
- Project has a sandbox/mock mode that can be leveraged for DB-free testing
  - 项目有可利用的沙箱/模拟模式进行无数据库测试
- Running `/bug-check` or similar review commands after code changes
  - 代码变更后运行 `/bug-check` 或类似的审查命令
- Multiple code paths exist (sandbox vs production, feature flags, etc.)
  - 存在多个代码路径（沙箱 vs 生产、功能开关等）

## The Core Problem

## The Core Problem
## 核心问题

When an AI writes code and then reviews its own work, it carries the same assumptions into both steps. This creates a predictable failure pattern:
当 AI 编写代码然后审查自己的工作时，它会将相同的假设带到两个步骤中。这创建了一个可预测的失败模式：

```
AI writes fix → AI reviews fix → AI says "looks correct" → Bug still exists
AI 写修复 → AI 审查修复 → AI 说"看起来正确" → Bug 仍然存在
```

**Real-world example** (observed in production):
**真实案例**（在生产环境中观察到）：

```
Fix 1: Added notification_settings to API response
修复 1: 在 API 响应中添加了 notification_settings
  → Forgot to add it to the SELECT query
  → 忘记在 SELECT 查询中添加它
  → AI reviewed and missed it (same blind spot)
  → AI 审查时遗漏了（相同的盲点）

Fix 2: Added it to SELECT query
修复 2: 在 SELECT 查询中添加了它
  → TypeScript build error (column not in generated types)
  → TypeScript 构建错误（生成的类型中没有该列）
  → AI reviewed Fix 1 but didn't catch the SELECT issue
  → AI 审查了修复 1 但没有捕获 SELECT 问题

Fix 3: Changed to SELECT *
修复 3: 改为 SELECT *
  → Fixed production path, forgot sandbox path
  → 修复了生产路径，忘记了沙箱路径
  → AI reviewed and missed it AGAIN (4th occurrence)
  → AI 再次审查时遗漏了（第 4 次发生）

Fix 4: Test caught it instantly on first run ✅
修复 4: 测试在首次运行时立即捕获 ✅
```

The pattern: **sandbox/production path inconsistency** is the #1 AI-introduced regression.
这个模式：**沙箱/生产路径不一致**是 AI 引入的 #1 回归问题。

## Sandbox-Mode API Testing

## Sandbox-Mode API Testing
## 沙箱模式 API 测试

Most projects with AI-friendly architecture have a sandbox/mock mode. This is the key to fast, DB-free API testing.
大多数 AI 友好架构的项目都有沙箱/模拟模式。这是快速、无数据库 API 测试的关键。

### Setup (Vitest + Next.js App Router)

### Setup (Vitest + Next.js App Router)
### 设置（Vitest + Next.js App Router）

```typescript
// vitest.config.ts
import { defineConfig } from "vitest/config";
import path from "path";

export default defineConfig({
  test: {
    environment: "node",
    globals: true,
    include: ["__tests__/**/*.test.ts"],
    setupFiles: ["__tests__/setup.ts"],
  },
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "."),
    },
  },
});
```

```typescript
// __tests__/setup.ts
// Force sandbox mode — no database needed
// 强制沙箱模式——不需要数据库
process.env.SANDBOX_MODE = "true";
process.env.NEXT_PUBLIC_SUPABASE_URL = "";
process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY = "";
```

### Test Helper for Next.js API Routes

### Test Helper for Next.js API Routes
### Next.js API 路由的测试辅助函数

```typescript
// __tests__/helpers.ts
import { NextRequest } from "next/server";

export function createTestRequest(
  url: string,
  options?: {
    method?: string;
    body?: Record<string, unknown>;
    headers?: Record<string, string>;
    sandboxUserId?: string;
  },
): NextRequest {
  const { method = "GET", body, headers = {}, sandboxUserId } = options || {};
  const fullUrl = url.startsWith("http") ? url : `http://localhost:3000${url}`;
  const reqHeaders: Record<string, string> = { ...headers };

  if (sandboxUserId) {
    reqHeaders["x-sandbox-user-id"] = sandboxUserId;
  }

  const init: { method: string; headers: Record<string, string>; body?: string } = {
    method,
    headers: reqHeaders,
  };

  if (body) {
    init.body = JSON.stringify(body);
    reqHeaders["content-type"] = "application/json";
  }

  return new NextRequest(fullUrl, init);
}

export async function parseResponse(response: Response) {
  const json = await response.json();
  return { status: response.status, json };
}
```

### Writing Regression Tests

### Writing Regression Tests
### 编写回归测试

The key principle: **write tests for bugs that were found, not for code that works**.
关键原则：**为发现的 bug 编写测试，而非为正常工作的代码编写测试**。

```typescript
// __tests__/api/user/profile.test.ts
import { describe, it, expect } from "vitest";
import { createTestRequest, parseResponse } from "../../helpers";
import { GET, PATCH } from "@/app/api/user/profile/route";

// Define the contract — what fields MUST be in the response
// 定义契约——响应中必须包含哪些字段
const REQUIRED_FIELDS = [
  "id",
  "email",
  "full_name",
  "phone",
  "role",
  "created_at",
  "avatar_url",
  "notification_settings",  // ← Added after bug found it missing
  // ← 在发现缺失后添加
];

describe("GET /api/user/profile", () => {
  it("returns all required fields", async () => {
    const req = createTestRequest("/api/user/profile");
    const res = await GET(req);
    const { status, json } = await parseResponse(res);

    expect(status).toBe(200);
    for (const field of REQUIRED_FIELDS) {
      expect(json.data).toHaveProperty(field);
    }
  });

  // Regression test — this exact bug was introduced by AI 4 times
  // 回归测试——这个 bug 被 AI 引入 4 次
  it("notification_settings is not undefined (BUG-R1 regression)", async () => {
    const req = createTestRequest("/api/user/profile");
    const res = await GET(req);
    const { json } = await parseResponse(res);

    expect("notification_settings" in json.data).toBe(true);
    const ns = json.data.notification_settings;
    expect(ns === null || typeof ns === "object").toBe(true);
  });
});
```

### Testing Sandbox/Production Parity

### Testing Sandbox/Production Parity
### 测试沙箱/生产一致性

The most common AI regression: fixing production path but forgetting sandbox path (or vice versa).
最常见的 AI 回归：修复生产路径但忘记沙箱路径（反之亦然）。

```typescript
// Test that sandbox responses match the expected contract
// 测试沙箱响应是否符合预期契约
describe("GET /api/user/messages (conversation list)", () => {
  it("includes partner_name in sandbox mode", async () => {
    const req = createTestRequest("/api/user/messages", {
      sandboxUserId: "user-001",
    });
    const res = await GET(req);
    const { json } = await parseResponse(res);

    // This caught a bug where partner_name was added
    // to production path but not sandbox path
    // 这捕获了一个 bug：partner_name 被添加到生产路径但没有添加到沙箱路径
    if (json.data.length > 0) {
      for (const conv of json.data) {
        expect("partner_name" in conv).toBe(true);
      }
    }
  });
});
```

## Integrating Tests into Bug-Check Workflow

## Integrating Tests into Bug-Check Workflow
## 将测试集成到 Bug-Check 工作流

### Custom Command Definition

### Custom Command Definition
### 自定义命令定义

```markdown
<!-- .claude/commands/bug-check.md -->
# Bug Check

## Step 1: Automated Tests (mandatory, cannot skip)
## Step 1: 自动化测试（强制要求，不能跳过）

Run these commands FIRST before any code review:
在任何代码审查之前首先运行这些命令：

    npm run test       # Vitest test suite
    npm run build      # TypeScript type check + build

- If tests fail → report as highest priority bug
  - 如果测试失败 → 报告为最高优先级 bug
- If build fails → report type errors as highest priority
  - 如果构建失败 → 报告类型错误为最高优先级
- Only proceed to Step 2 if both pass
  - 仅在两者都通过时继续步骤 2

## Step 2: Code Review (AI review)
## Step 2: 代码审查（AI 审查）

1. Sandbox / production path consistency
  1. 沙箱/生产路径一致性
2. API response shape matches frontend expectations
  2. API 响应结构与前端期望匹配
3. SELECT clause completeness
  3. SELECT 子句完整性
4. Error handling with rollback
  4. 带回滚的错误处理
5. Optimistic update race conditions
  5. 乐观更新竞态条件

## Step 3: For each bug fixed, propose a regression test
## Step 3: 对于每个修复的 bug，提出一个回归测试
```

### The Workflow

### The Workflow
### 工作流

```
User: "バグチェックして" (or "/bug-check")
用户："バグチェックして"（或 "/bug-check"）
  │
  ├─ Step 1: npm run test
  │   ├─ FAIL → Bug found mechanically (no AI judgment needed)
  │   └─ PASS → Continue
  ├─ 步骤 1: npm run test
  │   ├─ 失败 → 机械地发现 bug（无需 AI 判断）
  │   └─ 通过 → 继续

  ├─ Step 2: npm run build
  │   ├─ FAIL → Type error found mechanically
  │   └─ PASS → Continue
  ├─ 步骤 2: npm run build
  │   ├─ 失败 → 机械地发现类型错误
  │   └─ 通过 → 继续

  ├─ Step 3: AI code review (with known blind spots in mind)
  │   └─ Findings reported
  ├─ 步骤 3: AI 代码审查（考虑已知的盲点）
  │   └─ 报告发现

  └─ Step 4: For each fix, write a regression test
      └─ Next bug-check catches if fix breaks
  └─ 步骤 4: 对于每个修复，编写一个回归测试
      └─ 下一次 bug 检查会捕获修复是否破坏
```

## Common AI Regression Patterns

## Common AI Regression Patterns
## 常见 AI 回归模式

### Pattern 1: Sandbox/Production Path Mismatch

### Pattern 1: Sandbox/Production Path Mismatch
### 模式 1: 沙箱/生产路径不匹配

**Frequency**: Most common (observed in 3 out of 4 regressions)
**频率**：最常见（在 4 次回归中观察到 3 次）

```typescript
// ❌ AI adds field to production path only
// ❌ AI 仅在生产路径中添加字段
if (isSandboxMode()) {
  return { data: { id, email, name } };  // Missing new field
  // 缺少新字段
}
// Production path
// 生产路径
return { data: { id, email, name, notification_settings } };

// ✅ Both paths must return the same shape
// ✅ 两个路径必须返回相同的结构
if (isSandboxMode()) {
  return { data: { id, email, name, notification_settings: null } };
}
return { data: { id, email, name, notification_settings } };
```

**Test to catch it**:
**捕获它的测试**：

```typescript
it("sandbox and production return same fields", async () => {
  // In test env, sandbox mode is forced ON
  // 在测试环境中，沙箱模式被强制开启
  const res = await GET(createTestRequest("/api/user/profile"));
  const { json } = await parseResponse(res);

  for (const field of REQUIRED_FIELDS) {
    expect(json.data).toHaveProperty(field);
  }
});
```

### Pattern 2: SELECT Clause Omission

### Pattern 2: SELECT Clause Omission
### 模式 2: SELECT 子句遗漏

**Frequency**: Common with Supabase/Prisma when adding new columns
**频率**：在使用 Supabase/Prisma 添加新列时常见

```typescript
// ❌ New column added to response but not to SELECT
// ❌ 新列被添加到响应但没有添加到 SELECT
const { data } = await supabase
  .from("users")
  .select("id, email, name")  // notification_settings not here
  // notification_settings 不在这里
  .single();

return { data: { ...data, notification_settings: data.notification_settings } };
// → notification_settings is always undefined
// → notification_settings 始终是 undefined

// ✅ Use SELECT * or explicitly include new columns
// ✅ 使用 SELECT * 或显式包含新列
const { data } = await supabase
  .from("users")
  .select("*")
  .single();
```

### Pattern 3: Error State Leakage

### Pattern 3: Error State Leakage
### 模式 3: 错误状态泄漏

**Frequency**: Moderate — when adding error handling to existing components
**频率**：中等——在为现有组件添加错误处理时

```typescript
// ❌ Error state set but old data not cleared
// ❌ 设置了错误状态但旧数据没有清除
catch (err) {
  setError("Failed to load");
  // reservations still shows data from previous tab!
  // reservations 仍然显示前一个标签页的数据！
}

// ✅ Clear related state on error
// ✅ 在错误时清除相关状态
catch (err) {
  setReservations([]);  // Clear stale data
  // 清除陈旧数据
  setError("Failed to load");
}
```

### Pattern 4: Optimistic Update Without Proper Rollback

### Pattern 4: Optimistic Update Without Proper Rollback
### 模式 4: 没有正确回滚的乐观更新

```typescript
// ❌ No rollback on failure
// ❌ 失败时没有回滚
const handleRemove = async (id: string) => {
  setItems(prev => prev.filter(i => i.id !== id));
  await fetch(`/api/items/${id}`, { method: "DELETE" });
  // If API fails, item is gone from UI but still in DB
  // 如果 API 失败，项目从 UI 中消失但仍在 DB 中
};

// ✅ Capture previous state and rollback on failure
// ✅ 捕获之前的状态并在失败时回滚
const handleRemove = async (id: string) => {
  const prevItems = [...items];
  setItems(prev => prev.filter(i => i.id !== id));
  try {
    const res = await fetch(`/api/items/${id}`, { method: "DELETE" });
    if (!res.ok) throw new Error("API error");
  } catch {
    setItems(prevItems);  // Rollback
    // 回滚
    alert("削除に失敗しました");
  }
};
```

## Strategy: Test Where Bugs Were Found

## Strategy: Test Where Bugs Were Found
## 策略：在发现 bug 的地方测试

Don't aim for 100% coverage. Instead:
不要追求 100% 覆盖率。而是：

```
Bug found in /api/user/profile     → Write test for profile API
Bug found in /api/user/messages    → Write test for messages API
Bug found in /api/user/favorites   → Write test for favorites API
No bug in /api/user/notifications  → Don't write test (yet)
在 /api/user/profile 中发现 bug     → 为 profile API 编写测试
在 /api/user/messages 中发现 bug    → 为 messages API 编写测试
在 /api/user/favorites 中发现 bug   → 为 favorites API 编写测试
在 /api/user/notifications 中没有 bug → 暂不编写测试
```

**Why this works with AI development:**
**为什么这在 AI 开发中有效：**

1. AI tends to make the **same category of mistake** repeatedly
  AI 倾向于反复犯**同一类错误**
2. Bugs cluster in complex areas (auth, multi-path logic, state management)
  Bug 聚集在复杂领域（认证、多路径逻辑、状态管理）
3. Once tested, that exact regression **cannot happen again**
  一旦测试过，该回归**不可能再次发生**
4. Test count grows organically with bug fixes — no wasted effort
  测试数量随 bug 修复有机增长——没有浪费的努力

## Quick Reference

## Quick Reference
## 快速参考

| AI Regression Pattern | Test Strategy | Priority |
|---|---|---|
| Sandbox/production mismatch | Assert same response shape in sandbox mode | 🔴 High |
| SELECT clause omission | Assert all required fields in response | 🔴 High |
| Error state leakage | Assert state cleanup on error | 🟡 Medium |
| Missing rollback | Assert state restored on API failure | 🟡 Medium |
| Type cast masking null | Assert field is not undefined | 🟡 Medium |
|---|---|---|
| AI 回归模式 | 测试策略 | 优先级 |
| 沙箱/生产不匹配 | 在沙箱模式下断言相同的响应结构 | 🔴 高 |
| SELECT 子句遗漏 | 断言响应中包含所有必需字段 | 🔴 高 |
| 错误状态泄漏 | 断言错误时的状态清理 | 🟡 中 |
| 缺少回滚 | 断言 API 失败时状态恢复 | 🟡 中 |
| 类型转换掩盖 null | 断言字段不是 undefined | 🟡 中 |

## DO / DON'T

## DO / DON'T
## 宜/忌

**DO:**
**宜：**
- Write tests immediately after finding a bug (before fixing it if possible)
  - 在发现 bug 后立即编写测试（如果可能，在修复之前）
- Test the API response shape, not the implementation
  - 测试 API 响应结构，而非实现
- Run tests as the first step of every bug-check
  - 在每次 bug 检查时将测试作为第一步运行
- Keep tests fast (< 1 second total with sandbox mode)
  - 保持测试快速（使用沙箱模式总共 < 1 秒）
- Name tests after the bug they prevent (e.g., "BUG-R1 regression")
  - 用它们防止的 bug 命名测试（例如"BUG-R1 回归"）

**DON'T:**
**忌：**
- Write tests for code that has never had a bug
  - 为从未有过 bug 的代码编写测试
- Trust AI self-review as a substitute for automated tests
  - 将 AI 自我审查作为自动化测试的替代
- Skip sandbox path testing because "it's just mock data"
  - 因为"只是模拟数据"而跳过沙箱路径测试
- Write integration tests when unit tests suffice
  - 在单元测试足够时编写集成测试
- Aim for coverage percentage — aim for regression prevention
  - 以覆盖率为目标——以防止回归为目标
