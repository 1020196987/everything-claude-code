---
name: TypeScript Patterns
name: TypeScript 模式
description: TypeScript/JavaScript design patterns
description: TypeScript/JavaScript 设计模式
paths:
  - "**/*.ts"
  - "**/*.tsx"
  - "**/*.js"
  - "**/*.jsx"
---
# TypeScript/JavaScript Patterns
# TypeScript/JavaScript 模式

> This file extends [common/patterns.md](../common/patterns.md) with TypeScript/JavaScript specific content.
> 此文件扩展了 [common/patterns.md](../common/patterns.md)，包含 TypeScript/JavaScript 特定内容。

## API Response Format
## API 响应格式

```typescript
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
```

## Custom Hooks Pattern
## 自定义 Hook 模式

```typescript
export function useDebounce<T>(value: T, delay: number): T {
  const [debouncedValue, setDebouncedValue] = useState<T>(value)

  useEffect(() => {
    const handler = setTimeout(() => setDebouncedValue(value), delay)
    return () => clearTimeout(handler)
  }, [value, delay])

  return debouncedValue
}
```

## Repository Pattern
## 仓储模式

```typescript
interface Repository<T> {
  findAll(filters?: Filters): Promise<T[]>
  findById(id: string): Promise<T | null>
  create(data: CreateDto): Promise<T>
  update(id: string, data: UpdateDto): Promise<T>
  delete(id: string): Promise<void>
}
```
