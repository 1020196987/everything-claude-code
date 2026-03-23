---
name: api-design
description: REST API design patterns including resource naming, status codes, pagination, filtering, error responses, versioning, and rate limiting for production APIs.
description zh-CN: REST API 设计模式，包括资源命名、状态码、分页、过滤、错误响应、版本控制和面向生产 API 的限流。
origin: ECC
---

# API Design Patterns

# API Design Patterns
# API 设计模式

Conventions and best practices for designing consistent, developer-friendly REST APIs.
为设计一致、对开发者友好的 REST API 而制定的约定和最佳实践。

## When to Activate

## When to Activate
## 何时激活

- Designing new API endpoints
  - 设计新的 API 端点
- Reviewing existing API contracts
  - 审查现有 API 契约
- Adding pagination, filtering, or sorting
  - 添加分页、过滤或排序
- Implementing error handling for APIs
  - 为 API 实现错误处理
- Planning API versioning strategy
  - 规划 API 版本控制策略
- Building public or partner-facing APIs
  - 构建面向公众或合作伙伴的 API

## Resource Design

## Resource Design
## 资源设计

### URL Structure

### URL Structure
### URL 结构

```
# Resources are nouns, plural, lowercase, kebab-case
# 资源是名词、复数、小写、kebab-case
GET    /api/v1/users
GET    /api/v1/users/:id
POST   /api/v1/users
PUT    /api/v1/users/:id
PATCH  /api/v1/users/:id
DELETE /api/v1/users/:id

# Sub-resources for relationships
# 子资源用于关系
GET    /api/v1/users/:id/orders
POST   /api/v1/users/:id/orders

# Actions that don't map to CRUD (use verbs sparingly)
# 不映射到 CRUD 的操作（谨慎使用动词）
POST   /api/v1/orders/:id/cancel
POST   /api/v1/auth/login
POST   /api/v1/auth/refresh
```

### Naming Rules

### Naming Rules
### 命名规则

```
# GOOD
# 好
/api/v1/team-members          # kebab-case for multi-word resources
/api/v1/team-members          # 多词资源使用 kebab-case
/api/v1/orders?status=active  # query params for filtering
/api/v1/orders?status=active  # 查询参数用于过滤
/api/v1/users/123/orders      # nested resources for ownership
/api/v1/users/123/orders      # 嵌套资源用于表示所有权

# BAD
# 差
/api/v1/getUsers              # verb in URL
/api/v1/getUsers              # URL 中使用动词
/api/v1/user                  # singular (use plural)
/api/v1/user                  # 单数（应使用复数）
/api/v1/team_members          # snake_case in URLs
/api/v1/team_members          # URL 中使用 snake_case
/api/v1/users/123/getOrders   # verb in nested resource
/api/v1/users/123/getOrders   # 嵌套资源中使用动词
```

## HTTP Methods and Status Codes

## HTTP Methods and Status Codes
## HTTP 方法和状态码

### Method Semantics

### Method Semantics
### 方法语义

| Method | Idempotent | Safe | Use For |
|--------|-----------|------|---------|
| GET | Yes | Yes | Retrieve resources |
| POST | No | No | Create resources, trigger actions |
| PUT | Yes | No | Full replacement of a resource |
| PATCH | No* | No | Partial update of a resource |
| DELETE | Yes | No | Remove a resource |
|---|---|---|---|
| 方法 | 幂等 | 安全 | 用途 |
| GET | 是 | 是 | 检索资源 |
| POST | 否 | 否 | 创建资源、触发操作 |
| PUT | 是 | 否 | 完全替换资源 |
| PATCH | 否* | 否 | 部分更新资源 |
| DELETE | 是 | 否 | 删除资源 |

*PATCH can be made idempotent with proper implementation
*PATCH 可以通过正确的实现变为幂等

### Status Code Reference

### Status Code Reference
### 状态码参考

```
# Success
# 成功
200 OK                    — GET, PUT, PATCH (with response body)
201 Created               — POST (include Location header)
204 No Content            — DELETE, PUT (no response body)

# Client Errors
# 客户端错误
400 Bad Request           — Validation failure, malformed JSON
401 Unauthorized          — Missing or invalid authentication
403 Forbidden             — Authenticated but not authorized
404 Not Found             — Resource doesn't exist
409 Conflict              — Duplicate entry, state conflict
422 Unprocessable Entity  — Semantically invalid (valid JSON, bad data)
429 Too Many Requests     — Rate limit exceeded

# Server Errors
# 服务器错误
500 Internal Server Error — Unexpected failure (never expose details)
502 Bad Gateway           — Upstream service failed
503 Service Unavailable   — Temporary overload, include Retry-After
```

### Common Mistakes

### Common Mistakes
### 常见错误

```
# BAD: 200 for everything
# 差：对所有情况都使用 200
{ "status": 200, "success": false, "error": "Not found" }

# GOOD: Use HTTP status codes semantically
# 好：语义化地使用 HTTP 状态码
HTTP/1.1 404 Not Found
{ "error": { "code": "not_found", "message": "User not found" } }

# BAD: 500 for validation errors
# 差：对验证错误使用 500
# GOOD: 400 or 422 with field-level details
# 好：使用 400 或 422 并包含字段级详情

# BAD: 200 for created resources
# 差：对创建的资源的响应使用 200
# GOOD: 201 with Location header
# 好：使用 201 并包含 Location 头
HTTP/1.1 201 Created
Location: /api/v1/users/abc-123
```

## Response Format

## Response Format
## 响应格式

### Success Response

### Success Response
### 成功响应

```json
{
  "data": {
    "id": "abc-123",
    "email": "alice@example.com",
    "name": "Alice",
    "created_at": "2025-01-15T10:30:00Z"
  }
}
```

### Collection Response (with Pagination)

### Collection Response (with Pagination)
### 集合响应（带分页）

```json
{
  "data": [
    { "id": "abc-123", "name": "Alice" },
    { "id": "def-456", "name": "Bob" }
  ],
  "meta": {
    "total": 142,
    "page": 1,
    "per_page": 20,
    "total_pages": 8
  },
  "links": {
    "self": "/api/v1/users?page=1&per_page=20",
    "next": "/api/v1/users?page=2&per_page=20",
    "last": "/api/v1/users?page=8&per_page=20"
  }
}
```

### Error Response

### Error Response
### 错误响应

```json
{
  "error": {
    "code": "validation_error",
    "message": "Request validation failed",
    "details": [
      {
        "field": "email",
        "message": "Must be a valid email address",
        "code": "invalid_format"
      },
      {
        "field": "age",
        "message": "Must be between 0 and 150",
        "code": "out_of_range"
      }
    ]
  }
}
```

### Response Envelope Variants

### Response Envelope Variants
### 响应包装器变体

```typescript
// Option A: Envelope with data wrapper (recommended for public APIs)
// 选项 A：带 data 包装器的信封（推荐用于公共 API）
interface ApiResponse<T> {
  data: T;
  meta?: PaginationMeta;
  links?: PaginationLinks;
}

interface ApiError {
  error: {
    code: string;
    message: string;
    details?: FieldError[];
  };
}

// Option B: Flat response (simpler, common for internal APIs)
// 选项 B：扁平响应（更简单，常见于内部 API）
// Success: just return the resource directly
// 成功：直接返回资源
// Error: return error object
// 错误：返回错误对象
// Distinguish by HTTP status code
// 通过 HTTP 状态码区分
```

## Pagination

## Pagination
## 分页

### Offset-Based (Simple)

### Offset-Based (Simple)
### 基于偏移量（简单）

```
GET /api/v1/users?page=2&per_page=20

# Implementation
# 实现
SELECT * FROM users
ORDER BY created_at DESC
LIMIT 20 OFFSET 20;
```

**Pros:** Easy to implement, supports "jump to page N"
**优点：** 易于实现，支持"跳转到第 N 页"
**Cons:** Slow on large offsets (OFFSET 100000), inconsistent with concurrent inserts
**缺点：** 在大偏移量时慢（OFFSET 100000），与并发插入不一致

### Cursor-Based (Scalable)

### Cursor-Based (Scalable)
### 基于游标（可扩展）

```
GET /api/v1/users?cursor=eyJpZCI6MTIzfQ&limit=20

# Implementation
# 实现
SELECT * FROM users
WHERE id > :cursor_id
ORDER BY id ASC
LIMIT 21;  -- fetch one extra to determine has_next
// 多获取一条以确定 has_next
```

```json
{
  "data": [...],
  "meta": {
    "has_next": true,
    "next_cursor": "eyJpZCI6MTQzfQ"
  }
}
```

**Pros:** Consistent performance regardless of position, stable with concurrent inserts
**优点：** 无论位置如何性能一致，与并发插入稳定
**Cons:** Cannot jump to arbitrary page, cursor is opaque
**缺点：** 无法跳转到任意页面，游标不透明

### When to Use Which

### When to Use Which
### 何时使用哪种

| Use Case | Pagination Type |
|----------|----------------|
| Admin dashboards, small datasets (<10K) | Offset |
| Infinite scroll, feeds, large datasets | Cursor |
| Public APIs | Cursor (default) with offset (optional) |
| Search results | Offset (users expect page numbers) |
|---|---|---|
| 使用场景 | 分页类型 |
| 管理后台，小数据集（<10K） | 偏移量 |
| 无限滚动、动态订阅、大数据集 | 游标 |
| 公共 API | 游标（默认）+ 偏移量（可选）|
| 搜索结果 | 偏移量（用户期望页码）|

## Filtering, Sorting, and Search

## Filtering, Sorting, and Search
## 过滤、排序和搜索

### Filtering

### Filtering
### 过滤

```
# Simple equality
# 简单相等
GET /api/v1/orders?status=active&customer_id=abc-123

# Comparison operators (use bracket notation)
# 比较运算符（使用括号表示法）
GET /api/v1/products?price[gte]=10&price[lte]=100
GET /api/v1/orders?created_at[after]=2025-01-01

# Multiple values (comma-separated)
# 多值（逗号分隔）
GET /api/v1/products?category=electronics,clothing

# Nested fields (dot notation)
# 嵌套字段（点号表示法）
GET /api/v1/orders?customer.country=US
```

### Sorting

### Sorting
### 排序

```
# Single field (prefix - for descending)
# 单字段（前缀 - 表示降序）
GET /api/v1/products?sort=-created_at

# Multiple fields (comma-separated)
# 多字段（逗号分隔）
GET /api/v1/products?sort=-featured,price,-created_at
```

### Full-Text Search

### Full-Text Search
### 全文搜索

```
# Search query parameter
# 搜索查询参数
GET /api/v1/products?q=wireless+headphones

# Field-specific search
# 字段特定搜索
GET /api/v1/users?email=alice
```

### Sparse Fieldsets

### Sparse Fieldsets
### 稀疏字段集

```
# Return only specified fields (reduces payload)
# 仅返回指定字段（减少负载）
GET /api/v1/users?fields=id,name,email
GET /api/v1/orders?fields=id,total,status&include=customer.name
```

## Authentication and Authorization

## Authentication and Authorization
## 认证和授权

### Token-Based Auth

### Token-Based Auth
### 基于令牌的身份验证

```
# Bearer token in Authorization header
# Authorization 头中的 Bearer 令牌
GET /api/v1/users
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...

# API key (for server-to-server)
# API 密钥（用于服务端到服务端）
GET /api/v1/data
X-API-Key: sk_live_abc123
```

### Authorization Patterns

### Authorization Patterns
### 授权模式

```typescript
// Resource-level: check ownership
// 资源级别：检查所有权
app.get("/api/v1/orders/:id", async (req, res) => {
  const order = await Order.findById(req.params.id);
  if (!order) return res.status(404).json({ error: { code: "not_found" } });
  if (order.userId !== req.user.id) return res.status(403).json({ error: { code: "forbidden" } });
  return res.json({ data: order });
});

// Role-based: check permissions
// 基于角色：检查权限
app.delete("/api/v1/users/:id", requireRole("admin"), async (req, res) => {
  await User.delete(req.params.id);
  return res.status(204).send();
});
```

## Rate Limiting

## Rate Limiting
## 限流

### Headers

### Headers
### 响应头

```
HTTP/1.1 200 OK
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1640000000

# When exceeded
# 超出限制时
HTTP/1.1 429 Too Many Requests
Retry-After: 60
{
  "error": {
    "code": "rate_limit_exceeded",
    "message": "Rate limit exceeded. Try again in 60 seconds."
  }
}
```

### Rate Limit Tiers

### Rate Limit Tiers
### 限流层级

| Tier | Limit | Window | Use Case |
|------|-------|--------|----------|
| Anonymous | 30/min | Per IP | Public endpoints |
| Authenticated | 100/min | Per user | Standard API access |
| Premium | 1000/min | Per API key | Paid API plans |
| Internal | 10000/min | Per service | Service-to-service |
|---|---|---|---|
| 层级 | 限制 | 窗口 | 使用场景 |
| 匿名 | 30/分钟 | 每 IP | 公共端点 |
| 已认证 | 100/分钟 | 每用户 | 标准 API 访问 |
| 高级 | 1000/分钟 | 每 API 密钥 | 付费 API 计划 |
| 内部 | 10000/分钟 | 每服务 | 服务间通信 |

## Versioning

## Versioning
## 版本控制

### URL Path Versioning (Recommended)

### URL Path Versioning (Recommended)
### URL 路径版本控制（推荐）

```
/api/v1/users
/api/v2/users
```

**Pros:** Explicit, easy to route, cacheable
**优点：** 明确、易于路由、可缓存
**Cons:** URL changes between versions
**缺点：** 版本间 URL 变化

### Header Versioning

### Header Versioning
### Header 版本控制

```
GET /api/users
Accept: application/vnd.myapp.v2+json
```

**Pros:** Clean URLs
**优点：** 干净的 URL
**Cons:** Harder to test, easy to forget
**缺点：** 更难测试，容易遗忘

### Versioning Strategy

### Versioning Strategy
### 版本控制策略

```
1. Start with /api/v1/ — don't version until you need to
2. Maintain at most 2 active versions (current + previous)
3. Deprecation timeline:
   - Announce deprecation (6 months notice for public APIs)
   - Add Sunset header: Sunset: Sat, 01 Jan 2026 00:00:00 GMT
   - Return 410 Gone after sunset date
4. Non-breaking changes don't need a new version:
   - Adding new fields to responses
   - Adding new optional query parameters
   - Adding new endpoints
5. Breaking changes require a new version:
   - Removing or renaming fields
   - Changing field types
   - Changing URL structure
   - Changing authentication method
```

## Implementation Patterns

## Implementation Patterns
## 实现模式

### TypeScript (Next.js API Route)

### TypeScript (Next.js API Route)
### TypeScript（Next.js API 路由）

```typescript
import { z } from "zod";
import { NextRequest, NextResponse } from "next/server";

const createUserSchema = z.object({
  email: z.string().email(),
  name: z.string().min(1).max(100),
});

export async function POST(req: NextRequest) {
  const body = await req.json();
  const parsed = createUserSchema.safeParse(body);

  if (!parsed.success) {
    return NextResponse.json({
      error: {
        code: "validation_error",
        message: "Request validation failed",
        details: parsed.error.issues.map(i => ({
          field: i.path.join("."),
          message: i.message,
          code: i.code,
        })),
      },
    }, { status: 422 });
  }

  const user = await createUser(parsed.data);

  return NextResponse.json(
    { data: user },
    {
      status: 201,
      headers: { Location: `/api/v1/users/${user.id}` },
    },
  );
}
```

### Python (Django REST Framework)

### Python (Django REST Framework)
### Python（Django REST Framework）

```python
from rest_framework import serializers, viewsets, status
from rest_framework.response import Response

class CreateUserSerializer(serializers.Serializer):
    email = serializers.EmailField()
    name = serializers.CharField(max_length=100)

class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ["id", "email", "name", "created_at"]

class UserViewSet(viewsets.ModelViewSet):
    serializer_class = UserSerializer
    permission_classes = [IsAuthenticated]

    def get_serializer_class(self):
        if self.action == "create":
            return CreateUserSerializer
        return UserSerializer

    def create(self, request):
        serializer = CreateUserSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        user = UserService.create(**serializer.validated_data)
        return Response(
            {"data": UserSerializer(user).data},
            status=status.HTTP_201_CREATED,
            headers={"Location": f"/api/v1/users/{user.id}"},
        )
```

### Go (net/http)

### Go (net/http)
### Go（net/http）

```go
func (h *UserHandler) CreateUser(w http.ResponseWriter, r *http.Request) {
    var req CreateUserRequest
    if err := json.NewDecoder(r.Body).Decode(&req); err != nil {
        writeError(w, http.StatusBadRequest, "invalid_json", "Invalid request body")
        return
    }

    if err := req.Validate(); err != nil {
        writeError(w, http.StatusUnprocessableEntity, "validation_error", err.Error())
        return
    }

    user, err := h.service.Create(r.Context(), req)
    if err != nil {
        switch {
        case errors.Is(err, domain.ErrEmailTaken):
            writeError(w, http.StatusConflict, "email_taken", "Email already registered")
        default:
            writeError(w, http.StatusInternalServerError, "internal_error", "Internal error")
        }
        return
    }

    w.Header().Set("Location", fmt.Sprintf("/api/v1/users/%s", user.ID))
    writeJSON(w, http.StatusCreated, map[string]any{"data": user})
}
```

## API Design Checklist

## API Design Checklist
## API 设计清单

Before shipping a new endpoint:
在发布新端点之前：

- [ ] Resource URL follows naming conventions (plural, kebab-case, no verbs)
  - [ ] 资源 URL 遵循命名约定（复数、kebab-case、无动词）
- [ ] Correct HTTP method used (GET for reads, POST for creates, etc.)
  - [ ] 使用正确的 HTTP 方法（GET 用于读取，POST 用于创建等）
- [ ] Appropriate status codes returned (not 200 for everything)
  - [ ] 返回适当的状态码（不要对所有情况都使用 200）
- [ ] Input validated with schema (Zod, Pydantic, Bean Validation)
  - [ ] 使用 schema 验证输入（Zod、Pydantic、Bean Validation）
- [ ] Error responses follow standard format with codes and messages
  - [ ] 错误响应遵循带有代码和消息的标准格式
- [ ] Pagination implemented for list endpoints (cursor or offset)
  - [ ] 列表端点实现分页（游标或偏移量）
- [ ] Authentication required (or explicitly marked as public)
  - [ ] 需要认证（或明确标记为公开）
- [ ] Authorization checked (user can only access their own resources)
  - [ ] 检查授权（用户只能访问自己的资源）
- [ ] Rate limiting configured
  - [ ] 配置限流
- [ ] Response does not leak internal details (stack traces, SQL errors)
  - [ ] 响应不泄露内部细节（堆栈跟踪、SQL 错误）
- [ ] Consistent naming with existing endpoints (camelCase vs snake_case)
  - [ ] 与现有端点命名一致（camelCase vs snake_case）
- [ ] Documented (OpenAPI/Swagger spec updated)
  - [ ] 已文档化（更新 OpenAPI/Swagger 规范）
