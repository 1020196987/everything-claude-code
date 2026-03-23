---
name: TypeScript Security
name: TypeScript 安全
description: TypeScript/JavaScript security guidelines
description: TypeScript/JavaScript 安全指南
paths:
  - "**/*.ts"
  - "**/*.tsx"
  - "**/*.js"
  - "**/*.jsx"
---
# TypeScript/JavaScript Security
# TypeScript/JavaScript 安全

> This file extends [common/security.md](../common/security.md) with TypeScript/JavaScript specific content.
> 此文件扩展了 [common/security.md](../common/security.md)，包含 TypeScript/JavaScript 特定内容。

## Secret Management
## 密钥管理

```typescript
// NEVER: Hardcoded secrets
// 永不使用：硬编码密钥
const apiKey = "sk-proj-xxxxx"

// ALWAYS: Environment variables
// 始终使用：环境变量
const apiKey = process.env.OPENAI_API_KEY

if (!apiKey) {
  throw new Error('OPENAI_API_KEY not configured')
}
```

## Agent Support
## Agent 支持

- Use **security-reviewer** skill for comprehensive security audits
- 使用 **security-reviewer** skill 进行全面的安全审计
