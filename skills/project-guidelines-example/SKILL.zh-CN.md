---
name: project-guidelines-example
description: "Example project-specific skill template based on a real production application."
description zh-CN: 基于真实生产应用的项目特定技能模板示例。
origin: ECC
---

# Project Guidelines Skill (Example)

## Project Guidelines Skill (Example)
## 项目指南技能（示例）

This is an example of a project-specific skill. Use this as a template for your own projects.
这是项目特定技能的示例。用作你自己项目的模板。

Based on a real production application: [Zenith](https://zenith.chat) - AI-powered customer discovery platform.
基于真实生产应用：[Zenith](https://zenith.chat) — AI驱动的客户发现平台。

## When to Use

## When to Use
## 何时使用

Reference this skill when working on the specific project it's designed for. Project skills contain:
在处理为此技能设计的特定项目时参考此技能。项目技能包含：
- Architecture overview
- 架构概览
- File structure
- 文件结构
- Code patterns
- 代码模式
- Testing requirements
- 测试要求
- Deployment workflow
- 部署工作流

---

## Architecture Overview

## Architecture Overview
## 架构概览

**Tech Stack:**
**技术栈：**
- **Frontend**: Next.js 15 (App Router), TypeScript, React
- **前端**：Next.js 15 (App Router)、TypeScript、React
- **Backend**: FastAPI (Python), Pydantic models
- **后端**：FastAPI (Python)、Pydantic 模型
- **Database**: Supabase (PostgreSQL)
- **数据库**：Supabase (PostgreSQL)
- **AI**: Claude API with tool calling and structured output
- **AI**：带工具调用和结构化输出的 Claude API
- **Deployment**: Google Cloud Run
- **部署**：Google Cloud Run
- **Testing**: Playwright (E2E), pytest (backend), React Testing Library
- **测试**：Playwright (E2E)、pytest (后端)、React Testing Library

**Services:**
**服务：**
```
┌─────────────────────────────────────────────────────────────┐
│                         Frontend                            │
│  Next.js 15 + TypeScript + TailwindCSS                     │
│  Deployed: Vercel / Cloud Run                              │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                         Backend                             │
│  FastAPI + Python 3.11 + Pydantic                          │
│  Deployed: Cloud Run                                       │
└─────────────────────────────────────────────────────────────┘
                              │
              ┌───────────────┼───────────────┐
              ▼               ▼               ▼
        ┌──────────┐   ┌──────────┐   ┌──────────┐
        │ Supabase │   │  Claude  │   │  Redis   │
        │ Database │   │   API    │   │  Cache   │
        └──────────┘   └──────────┘   └──────────┘
```

---

## File Structure

## File Structure
## 文件结构

```
project/
├── frontend/
│   └── src/
│       ├── app/              # Next.js app router pages
│       │   ├── api/          # API routes / API 路由
│       │   ├── (auth)/       # Auth-protected routes / 认证保护路由
│       │   └── workspace/    # Main app workspace / 主应用工作区
│       ├── components/       # React components
│       │   ├── ui/           # Base UI components / 基础 UI 组件
│       │   ├── forms/        # Form components / 表单组件
│       │   └── layouts/      # Layout components / 布局组件
│       ├── hooks/            # Custom React hooks
│       ├── lib/              # Utilities
│       ├── types/            # TypeScript definitions
│       └── config/           # Configuration
│
├── backend/
│   ├── routers/              # FastAPI route handlers
│   ├── models.py             # Pydantic models
│   ├── main.py               # FastAPI app entry
│   ├── auth_system.py        # Authentication / 认证
│   ├── database.py           # Database operations / 数据库操作
│   ├── services/             # Business logic / 业务逻辑
│   └── tests/                # pytest tests
│
├── deploy/                   # Deployment configs / 部署配置
├── docs/                     # Documentation / 文档
└── scripts/                  # Utility scripts / 实用脚本
```

---

## Code Patterns

## Code Patterns
## 代码模式

### API Response Format (FastAPI)

### API Response Format (FastAPI)
### API 响应格式（FastAPI）

```python
from pydantic import BaseModel
from typing import Generic, TypeVar, Optional

T = TypeVar('T')

class ApiResponse(BaseModel, Generic[T]):
    success: bool
    data: Optional[T] = None
    error: Optional[str] = None

    @classmethod
    def ok(cls, data: T) -> "ApiResponse[T]":
        return cls(success=True, data=data)

    @classmethod
    def fail(cls, error: str) -> "ApiResponse[T]":
        return cls(success=False, error=error)
```

### Frontend API Calls (TypeScript)

### Frontend API Calls (TypeScript)
### 前端 API 调用（TypeScript）

```typescript
interface ApiResponse<T> {
  success: boolean
  data?: T
  error?: string
}

async function fetchApi<T>(
  endpoint: string,
  options?: RequestInit
): Promise<ApiResponse<T>> {
  try {
    const response = await fetch(`/api${endpoint}`, {
      ...options,
      headers: {
        'Content-Type': 'application/json',
        ...options?.headers,
      },
    })

    if (!response.ok) {
      return { success: false, error: `HTTP ${response.status}` }
    }

    return await response.json()
  } catch (error) {
    return { success: false, error: String(error) }
  }
}
```

### Claude AI Integration (Structured Output)

### Claude AI Integration (Structured Output)
### Claude AI 集成（结构化输出）

```python
from anthropic import Anthropic
from pydantic import BaseModel

class AnalysisResult(BaseModel):
    summary: str
    key_points: list[str]
    confidence: float

async def analyze_with_claude(content: str) -> AnalysisResult:
    client = Anthropic()

    response = client.messages.create(
        model="claude-sonnet-4-5-20250514",
        max_tokens=1024,
        messages=[{"role": "user", "content": content}],
        tools=[{
            "name": "provide_analysis",
            "description": "Provide structured analysis",
            "input_schema": AnalysisResult.model_json_schema()
        }],
        tool_choice={"type": "tool", "name": "provide_analysis"}
    )

    # Extract tool use result
    # 提取工具使用结果
    tool_use = next(
        block for block in response.content
        if block.type == "tool_use"
    )

    return AnalysisResult(**tool_use.input)
```

### Custom Hooks (React)

### Custom Hooks (React)
### 自定义 Hook（React）

```typescript
import { useState, useCallback } from 'react'

interface UseApiState<T> {
  data: T | null
  loading: boolean
  error: string | null
}

export function useApi<T>(
  fetchFn: () => Promise<ApiResponse<T>>
) {
  const [state, setState] = useState<UseApiState<T>>({
    data: null,
    loading: false,
    error: null,
  })

  const execute = useCallback(async () => {
    setState(prev => ({ ...prev, loading: true, error: null }))

    const result = await fetchFn()

    if (result.success) {
      setState({ data: result.data!, loading: false, error: null })
    } else {
      setState({ data: null, loading: false, error: result.error! })
    }
  }, [fetchFn])

  return { ...state, execute }
}
```

---

## Testing Requirements

## Testing Requirements
## 测试要求

### Backend (pytest)

### Backend (pytest)
### 后端（pytest）

```bash
# Run all tests
# 运行所有测试
poetry run pytest tests/

# Run with coverage
# 带覆盖率运行
poetry run pytest tests/ --cov=. --cov-report=html

# Run specific test file
# 运行特定测试文件
poetry run pytest tests/test_auth.py -v
```

**Test structure:**
**测试结构：**
```python
import pytest
from httpx import AsyncClient
from main import app

@pytest.fixture
async def client():
    async with AsyncClient(app=app, base_url="http://test") as ac:
        yield ac

@pytest.mark.asyncio
async def test_health_check(client: AsyncClient):
    response = await client.get("/health")
    assert response.status_code == 200
    assert response.json()["status"] == "healthy"
```

### Frontend (React Testing Library)

### Frontend (React Testing Library)
### 前端（React Testing Library）

```bash
# Run tests
# 运行测试
npm run test

# Run with coverage
# 带覆盖率运行
npm run test -- --coverage

# Run E2E tests
# 运行 E2E 测试
npm run test:e2e
```

**Test structure:**
**测试结构：**
```typescript
import { render, screen, fireEvent } from '@testing-library/react'
import { WorkspacePanel } from './WorkspacePanel'

describe('WorkspacePanel', () => {
  it('renders workspace correctly', () => {
    render(<WorkspacePanel />)
    expect(screen.getByRole('main')).toBeInTheDocument()
  })

  it('handles session creation', async () => {
    render(<WorkspacePanel />)
    fireEvent.click(screen.getByText('New Session'))
    expect(await screen.findByText('Session created')).toBeInTheDocument()
  })
})
```

---

## Deployment Workflow

## Deployment Workflow
## 部署工作流

### Pre-Deployment Checklist

### Pre-Deployment Checklist
### 部署前检查清单

- [ ] All tests passing locally / 所有测试在本地通过
- [ ] `npm run build` succeeds (frontend) / `npm run build` 成功（前端）
- [ ] `poetry run pytest` passes (backend) / `poetry run pytest` 通过（后端）
- [ ] No hardcoded secrets / 无硬编码密钥
- [ ] Environment variables documented / 环境变量已记录
- [ ] Database migrations ready / 数据库迁移就绪

### Deployment Commands

### Deployment Commands
### 部署命令

```bash
# Build and deploy frontend
# 构建并部署前端
cd frontend && npm run build
gcloud run deploy frontend --source .

# Build and deploy backend
# 构建并部署后端
cd backend
gcloud run deploy backend --source .
```

### Environment Variables

### Environment Variables
### 环境变量

```bash
# Frontend (.env.local)
# 前端（.env.local）
NEXT_PUBLIC_API_URL=https://api.example.com
NEXT_PUBLIC_SUPABASE_URL=https://xxx.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJ...

# Backend (.env)
# 后端（.env）
DATABASE_URL=postgresql://...
ANTHROPIC_API_KEY=sk-ant-...
SUPABASE_URL=https://xxx.supabase.co
SUPABASE_KEY=eyJ...
```

---

## Critical Rules

## Critical Rules
## 关键规则

1. **No emojis** in code, comments, or documentation
1. **代码、注释或文档中不使用表情符号**
2. **Immutability** - never mutate objects or arrays
2. **不可变性** — 绝不修改对象或数组
3. **TDD** - write tests before implementation
3. **TDD** — 在实现前写测试
4. **80% coverage** minimum
4. **最低 80% 覆盖率**
5. **Many small files** - 200-400 lines typical, 800 max
5. **多小文件** — 典型 200-400 行，最多 800 行
6. **No console.log** in production code
6. **生产代码中不使用 console.log**
7. **Proper error handling** with try/catch
7. **使用 try/catch 进行正确的错误处理**
8. **Input validation** with Pydantic/Zod
8. **使用 Pydantic/Zod 进行输入验证**

---

## Related Skills

## Related Skills
## 相关技能

- `coding-standards.md` - General coding best practices / 通用编码最佳实践
- `backend-patterns.md` - API and database patterns / API 和数据库模式
- `frontend-patterns.md` - React and Next.js patterns / React 和 Next.js 模式
- `tdd-workflow/` - Test-driven development methodology / 测试驱动开发方法论
