---
name: documentation-lookup
description: Use up-to-date library and framework docs via Context7 MCP instead of training data. Activates for setup questions, API references, code examples, or when the user names a framework (e.g. React, Next.js, Prisma).
description zh-CN: 通过 Context7 MCP 使用最新的库和框架文档，而非训练数据。当用户提出设置问题、API 参考、代码示例或提到框架名称时激活（例如 React、Next.js、Prisma）。
origin: ECC
---

# Documentation Lookup (Context7)

## Documentation Lookup (Context7)
## 文档查询（Context7）

When the user asks about libraries, frameworks, or APIs, fetch current documentation via the Context7 MCP (tools `resolve-library-id` and `query-docs`) instead of relying on training data.
当用户询问库、框架或 API 时，通过 Context7 MCP（工具 `resolve-library-id` 和 `query-docs`）获取当前文档，而不是依赖训练数据。

## Core Concepts

## Core Concepts
## 核心概念

- **Context7**: MCP server that exposes live documentation; use it instead of training data for libraries and APIs.
  **Context7**：暴露实时文档的 MCP 服务器；用于库和 API 的训练数据替代。
- **resolve-library-id**: Returns Context7-compatible library IDs (e.g. `/vercel/next.js`) from a library name and query.
  **resolve-library-id**：从库名称和查询返回 Context7 兼容的库 ID（例如 `/vercel/next.js`）。
- **query-docs**: Fetches documentation and code snippets for a given library ID and question. Always call resolve-library-id first to get a valid library ID.
  **query-docs**：获取给定库 ID 和问题的文档和代码片段。首先调用 resolve-library-id 以获取有效的库 ID。

## When to use

## When to use
## 何时使用

Activate when the user:
当用户满足以下情况时激活：

- Asks setup or configuration questions (e.g. "How do I configure Next.js middleware?")
  提出设置或配置问题（例如"如何配置 Next.js 中间件？"）
- Requests code that depends on a library ("Write a Prisma query for...")
  请求依赖于库的代码（"为...编写 Prisma 查询"）
- Needs API or reference information ("What are the Supabase auth methods?")
  需要 API 或参考信息（"Supabase auth 方法有哪些？"）
- Mentions specific frameworks or libraries (React, Vue, Svelte, Express, Tailwind, Prisma, Supabase, etc.)
  提到特定框架或库（React、Vue、Svelte、Express、Tailwind、Prisma、Supabase 等）

Use this skill whenever the request depends on accurate, up-to-date behavior of a library, framework, or API. Applies across harnesses that have the Context7 MCP configured (e.g. Claude Code, Cursor, Codex).
无论何时，只要请求依赖于库、框架或 API 的准确最新行为，都使用此技能。适用于配置了 Context7 MCP 的 harness（例如 Claude Code、Cursor、Codex）。

## How it works

## How it works
## 工作原理

### Step 1: Resolve the Library ID

### Step 1: Resolve the Library ID
### 步骤 1：解析库 ID

Call the **resolve-library-id** MCP tool with:
使用以下参数调用 **resolve-library-id** MCP 工具：

- **libraryName**: The library or product name taken from the user's question (e.g. `Next.js`, `Prisma`, `Supabase`).
  **libraryName**：从用户问题中提取的库或产品名称（例如 `Next.js`、`Prisma`、`Supabase`）。
- **query**: The user's full question. This improves relevance ranking of results.
  **query**：用户的完整问题。这会提高结果的相关性排名。

You must obtain a Context7-compatible library ID (format `/org/project` or `/org/project/version`) before querying docs. Do not call query-docs without a valid library ID from this step.
在查询文档之前，必须获取 Context7 兼容的库 ID（格式 `/org/project` 或 `/org/project/version`）。不要在没有从此步骤获得有效库 ID 的情况下调用 query-docs。

### Step 2: Select the Best Match

### Step 2: Select the Best Match
### 步骤 2：选择最佳匹配

From the resolution results, choose one result using:
从解析结果中，使用以下条件选择一个结果：

- **Name match**: Prefer exact or closest match to what the user asked for.
  **名称匹配**：优先选择与用户询问内容完全匹配或最接近的。
- **Benchmark score**: Higher scores indicate better documentation quality (100 is highest).
  **基准分数**：分数越高表示文档质量越好（100 为最高）。
- **Source reputation**: Prefer High or Medium reputation when available.
  **来源声誉**：可用的，优先选择高或中的声誉。
- **Version**: If the user specified a version (e.g. "React 19", "Next.js 15"), prefer a version-specific library ID if listed (e.g. `/org/project/v1.2.0`).
  **版本**：如果用户指定了版本（例如"React 19"、"Next.js 15"），如果列出，优先选择特定版本的库 ID（例如 `/org/project/v1.2.0`）。

### Step 3: Fetch the Documentation

### Step 3: Fetch the Documentation
### 步骤 3：获取文档

Call the **query-docs** MCP tool with:
使用以下参数调用 **query-docs** MCP 工具：

- **libraryId**: The selected Context7 library ID from Step 2 (e.g. `/vercel/next.js`).
  **libraryId**：从步骤 2 选择的 Context7 库 ID（例如 `/vercel/next.js`）。
- **query**: The user's specific question or task. Be specific to get relevant snippets.
  **query**：用户具体的问题或任务。要具体以获得相关片段。

Limit: do not call query-docs (or resolve-library-id) more than 3 times per question. If the answer is unclear after 3 calls, state the uncertainty and use the best information you have rather than guessing.
限制：每个问题调用 query-docs（或 resolve-library-id）不超过 3 次。如果 3 次调用后答案仍不清楚，说明不确定性并使用你拥有的最佳信息，而不是猜测。

### Step 4: Use the Documentation

### Step 4: Use the Documentation
### 步骤 4：使用文档

- Answer the user's question using the fetched, current information.
  使用获取到的最新信息回答用户的问题。
- Include relevant code examples from the docs when helpful.
  必要时包含文档中的相关代码示例。
- Cite the library or version when it matters (e.g. "In Next.js 15...").
  在重要时引用库或版本（例如"In Next.js 15..."）。

## Examples

## Examples
## 示例

### Example: Next.js middleware

### Example: Next.js middleware
### 示例：Next.js 中间件

1. Call **resolve-library-id** with `libraryName: "Next.js"`, `query: "How do I set up Next.js middleware?"`.
   调用 **resolve-library-id**，参数为 `libraryName: "Next.js"`、`query: "How do I set up Next.js middleware?"`。
2. From results, pick the best match (e.g. `/vercel/next.js`) by name and benchmark score.
   从结果中，按名称和基准分数选择最佳匹配（例如 `/vercel/next.js`）。
3. Call **query-docs** with `libraryId: "/vercel/next.js"`, `query: "How do I set up Next.js middleware?"`.
   调用 **query-docs**，参数为 `libraryId: "/vercel/next.js"`、`query: "How do I set up Next.js middleware?"`。
4. Use the returned snippets and text to answer; include a minimal `middleware.ts` example from the docs if relevant.
   使用返回的片段和文本回答；如相关，包含来自文档的最简 `middleware.ts` 示例。

### Example: Prisma query

### Example: Prisma query
### 示例：Prisma 查询

1. Call **resolve-library-id** with `libraryName: "Prisma"`, `query: "How do I query with relations?"`.
   调用 **resolve-library-id**，参数为 `libraryName: "Prisma"`、`query: "How do I query with relations?"`。
2. Select the official Prisma library ID (e.g. `/prisma/prisma`).
   选择官方 Prisma 库 ID（例如 `/prisma/prisma`）。
3. Call **query-docs** with that `libraryId` and the query.
   使用该 `libraryId` 和查询调用 **query-docs**。
4. Return the Prisma Client pattern (e.g. `include` or `select`) with a short code snippet from the docs.
   返回 Prisma Client 模式（例如 `include` 或 `select`）以及来自文档的简短代码片段。

### Example: Supabase auth methods

### Example: Supabase auth methods
### 示例：Supabase auth 方法

1. Call **resolve-library-id** with `libraryName: "Supabase"`, `query: "What are the auth methods?"`.
   调用 **resolve-library-id**，参数为 `libraryName: "Supabase"`、`query: "What are the auth methods?"`。
2. Pick the Supabase docs library ID.
   选择 Supabase 文档库 ID。
3. Call **query-docs**; summarize the auth methods and show minimal examples from the fetched docs.
   调用 **query-docs**；总结 auth 方法并展示从获取文档中的最简示例。

## Best Practices

## Best Practices
## 最佳实践

- **Be specific**: Use the user's full question as the query where possible for better relevance.
  **要具体**：尽可能使用用户的完整问题作为查询以获得更好的相关性。
- **Version awareness**: When users mention versions, use version-specific library IDs from the resolve step when available.
  **版本意识**：当用户提到版本时，使用从解析步骤获得的可用的特定版本库 ID。
- **Prefer official sources**: When multiple matches exist, prefer official or primary packages over community forks.
  **优先官方来源**：当存在多个匹配时，优先选择官方或主要包而非社区分支。
- **No sensitive data**: Redact API keys, passwords, tokens, and other secrets from any query sent to Context7. Treat the user's question as potentially containing secrets before passing it to resolve-library-id or query-docs.
  **无敏感数据**：从发送到 Context7 的任何查询中删除 API 密钥、密码、令牌和其他密钥。在将用户问题传递给 resolve-library-id 或 query-docs 之前，将其视为可能包含密钥。
