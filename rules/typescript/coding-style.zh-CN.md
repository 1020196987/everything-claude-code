---
name: TypeScript Coding Style
name: TypeScript 编码风格
description: TypeScript/JavaScript coding style guidelines
description: TypeScript/JavaScript 编码风格规范
paths:
  - "**/*.ts"
  - "**/*.tsx"
  - "**/*.js"
  - "**/*.jsx"
---
# TypeScript/JavaScript Coding Style
# TypeScript/JavaScript 编码风格

> This file extends [common/coding-style.md](../common/coding-style.md) with TypeScript/JavaScript specific content.
> 此文件扩展了 [common/coding-style.md](../common/coding-style.md)，包含 TypeScript/JavaScript 特定内容。

## Types and Interfaces
## 类型和接口

Use types to make public APIs, shared models, and component props explicit, readable, and reusable.
使用类型使公共 API、共享模型和组件 props 明确、可读和可重用。

### Public APIs
### 公共 API

- Add parameter and return types to exported functions, shared utilities, and public class methods
- 为导出的函数、共享工具和公共类方法添加参数和返回类型
- Let TypeScript infer obvious local variable types
- 让 TypeScript 推断明显的局部变量类型
- Extract repeated inline object shapes into named types or interfaces
- 将重复的内联对象形状提取为命名类型或接口

```typescript
// WRONG: Exported function without explicit types
// 错误：没有显式类型的导出函数
export function formatUser(user) {
  return `${user.firstName} ${user.lastName}`
}

// CORRECT: Explicit types on public APIs
// 正确：公共 API 上的显式类型
interface User {
  firstName: string
  lastName: string
}

export function formatUser(user: User): string {
  return `${user.firstName} ${user.lastName}`
}
```

### Interfaces vs. Type Aliases
### 接口 vs. 类型别名

- Use `interface` for object shapes that may be extended or implemented
- 对可能被扩展或实现的对象形状使用 `interface`
- Use `type` for unions, intersections, tuples, mapped types, and utility types
- 对联合、交叉类型、元组、映射类型和工具类型使用 `type`
- Prefer string literal unions over `enum` unless an `enum` is required for interoperability
- 优先使用字符串字面量联合，而非 `enum`，除非需要 `enum` 进行互操作

```typescript
interface User {
  id: string
  email: string
}

type UserRole = 'admin' | 'member'
type UserWithRole = User & {
  role: UserRole
}
```

### Avoid `any`
### 避免 `any`

- Avoid `any` in application code
- 在应用代码中避免使用 `any`
- Use `unknown` for external or untrusted input, then narrow it safely
- 对外部或不受信任的输入使用 `unknown`，然后安全地收窄
- Use generics when a value's type depends on the caller
- 当值的类型取决于调用者时使用泛型

```typescript
// WRONG: any removes type safety
// 错误：any 移除了类型安全
function getErrorMessage(error: any) {
  return error.message
}

// CORRECT: unknown forces safe narrowing
// 正确：unknown 强制安全收窄
function getErrorMessage(error: unknown): string {
  if (error instanceof Error) {
    return error.message
  }

  return 'Unexpected error'
}
```

### React Props
### React Props

- Define component props with a named `interface` or `type`
- 使用命名的 `interface` 或 `type` 定义组件 props
- Type callback props explicitly
- 显式类型化回调 props
- Do not use `React.FC` unless there is a specific reason to do so
- 除非有特定原因，否则不要使用 `React.FC`

```typescript
interface User {
  id: string
  email: string
}

interface UserCardProps {
  user: User
  onSelect: (id: string) => void
}

function UserCard({ user, onSelect }: UserCardProps) {
  return <button onClick={() => onSelect(user.id)}>{user.email}</button>
}
```

### JavaScript Files
### JavaScript 文件

- In `.js` and `.jsx` files, use JSDoc when types improve clarity and a TypeScript migration is not practical
- 在 `.js` 和 `.jsx` 文件中，当类型可以提高清晰度且 TypeScript 迁移不可行时使用 JSDoc
- Keep JSDoc aligned with runtime behavior
- 保持 JSDoc 与运行时行为一致

```javascript
/**
 * @param {{ firstName: string, lastName: string }} user
 * @returns {string}
 */
export function formatUser(user) {
  return `${user.firstName} ${user.lastName}`
}
```

## Immutability
## 不可变性

Use spread operator for immutable updates:
使用展开运算符进行不可变更新：

```typescript
interface User {
  id: string
  name: string
}

// WRONG: Mutation
// 错误：变更
function updateUser(user: User, name: string): User {
  user.name = name // MUTATION!
  return user
}

// CORRECT: Immutability
// 正确：不可变性
function updateUser(user: Readonly<User>, name: string): User {
  return {
    ...user,
    name
  }
}
```

## Error Handling
## 错误处理

Use async/await with try-catch and narrow unknown errors safely:
使用 async/await 配合 try-catch 并安全地收窄未知错误：

```typescript
interface User {
  id: string
  email: string
}

declare function riskyOperation(userId: string): Promise<User>

function getErrorMessage(error: unknown): string {
  if (error instanceof Error) {
    return error.message
  }

  return 'Unexpected error'
}

const logger = {
  error: (message: string, error: unknown) => {
    // Replace with your production logger (for example, pino or winston).
    // Replace with your production logger (for example, pino or winston).
  }
}

async function loadUser(userId: string): Promise<User> {
  try {
    const result = await riskyOperation(userId)
    return result
  } catch (error: unknown) {
    logger.error('Operation failed', error)
    throw new Error(getErrorMessage(error))
  }
}
```

## Input Validation
## 输入验证

Use Zod for schema-based validation and infer types from the schema:
使用 Zod 进行基于模式的验证并从模式推断类型：

```typescript
import { z } from 'zod'

const userSchema = z.object({
  email: z.string().email(),
  age: z.number().int().min(0).max(150)
})

type UserInput = z.infer<typeof userSchema>

const validated: UserInput = userSchema.parse(input)
```

## Console.log
## Console.log

- No `console.log` statements in production code
- 生产代码中不使用 `console.log` 语句
- Use proper logging libraries instead
- 改为使用适当的日志库
- See hooks for automatic detection
- 参见钩子了解自动检测
