---
name: mcp-server-patterns
description: Build MCP servers with Node/TypeScript SDK — tools, resources, prompts, Zod validation, stdio vs Streamable HTTP. Use Context7 or official MCP docs for latest API.
description zh-CN: 使用 Node/TypeScript SDK 构建 MCP 服务器 — 工具、资源、提示、Zod 验证、stdio vs Streamable HTTP。使用 Context7 或官方 MCP 文档获取最新 API。
origin: ECC
---

# MCP Server Patterns

## MCP Server Patterns
## MCP 服务器模式

The Model Context Protocol (MCP) lets AI assistants call tools, read resources, and use prompts from your server. Use this skill when building or maintaining MCP servers. The SDK API evolves; check Context7 (query-docs for "MCP") or the official MCP documentation for current method names and signatures.
模型上下文协议（MCP）让 AI 助手从你的服务器调用工具、读取资源和使用提示。在构建或维护 MCP 服务器时使用此技能。SDK API 不断演进；检查 Context7（query-docs for "MCP"）或官方 MCP 文档以获取当前的方法名称和签名。

## When to Use

## When to Use
## 何时使用

Use when: implementing a new MCP server, adding tools or resources, choosing stdio vs HTTP, upgrading the SDK, or debugging MCP registration and transport issues.
使用场景：实现新的 MCP 服务器、添加工具或资源、选择 stdio vs HTTP、升级 SDK 或调试 MCP 注册和传输问题。

## How It Works

## How It Works
## 工作原理

### Core concepts

### Core concepts
### 核心概念

- **Tools**: Actions the model can invoke (e.g. search, run a command). Register with `registerTool()` or `tool()` depending on SDK version.
  - **工具**：模型可以调用的操作（例如搜索、运行命令）。根据 SDK 版本使用 `registerTool()` 或 `tool()` 注册。
- **Resources**: Read-only data the model can fetch (e.g. file contents, API responses). Register with `registerResource()` or `resource()`. Handlers typically receive a `uri` argument.
  - **资源**：模型可以获取的只读数据（例如文件内容、API 响应）。使用 `registerResource()` 或 `resource()` 注册。处理程序通常接收 `uri` 参数。
- **Prompts**: Reusable, parameterised prompt templates the client can surface (e.g. in Claude Desktop). Register with `registerPrompt()` or equivalent.
  - **提示**：可重用的参数化提示模板，客户端可以呈现（例如在 Claude Desktop 中）。使用 `registerPrompt()` 或等效方法注册。
- **Transport**: stdio for local clients (e.g. Claude Desktop); Streamable HTTP is preferred for remote (Cursor, cloud). Legacy HTTP/SSE is for backward compatibility.
  - **传输**：stdio 用于本地客户端（例如 Claude Desktop）；Streamable HTTP 是远程（Cursor、云）的首选。传统 HTTP/SSE 用于向后兼容。

The Node/TypeScript SDK may expose `tool()` / `resource()` or `registerTool()` / `registerResource()`; the official SDK has changed over time. Always verify against the current [MCP docs](https://modelcontextprotocol.io) or Context7.
Node/TypeScript SDK 可能暴露 `tool()` / `resource()` 或 `registerTool()` / `registerResource()`；官方 SDK 随时间变化。始终对照当前 [MCP 文档](https://modelcontextprotocol.io) 或 Context7 进行验证。

### Connecting with stdio

### Connecting with stdio
### 使用 stdio 连接

For local clients, create a stdio transport and pass it to your server's connect method. The exact API varies by SDK version (e.g. constructor vs factory). See the official MCP documentation or query Context7 for "MCP stdio server" for the current pattern.
对于本地客户端，创建 stdio 传输并将其传递给服务器的 connect 方法。确切的 API 因 SDK 版本而异（例如构造函数 vs 工厂方法）。参见官方 MCP 文档或查询 Context7 获取"MCP stdio server"的当前模式。

Keep server logic (tools + resources) independent of transport so you can plug in stdio or HTTP in the entrypoint.
将服务器逻辑（工具 + 资源）与传输分离，这样你可以在入口点插入 stdio 或 HTTP。

### Remote (Streamable HTTP)

### Remote (Streamable HTTP)
### 远程（Streamable HTTP）

For Cursor, cloud, or other remote clients, use **Streamable HTTP** (single MCP HTTP endpoint per current spec). Support legacy HTTP/SSE only when backward compatibility is required.
对于 Cursor、云或其他远程客户端，使用 **Streamable HTTP**（根据当前规范为单个 MCP HTTP 端点）。仅在需要向后兼容时才支持传统 HTTP/SSE。

## Examples

## Examples
## 示例

### Install and server setup

### Install and server setup
### 安装和服务器设置

```bash
npm install @modelcontextprotocol/sdk zod
```

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { z } from "zod";

const server = new McpServer({ name: "my-server", version: "1.0.0" });
```

Register tools and resources using the API your SDK version provides: some versions use `server.tool(name, description, schema, handler)` (positional args), others use `server.tool({ name, description, inputSchema }, handler)` or `registerTool()`. Same for resources — include a `uri` in the handler when the API provides it. Check the official MCP docs or Context7 for the current `@modelcontextprotocol/sdk` signatures to avoid copy-paste errors.
使用 SDK 版本提供的 API 注册工具和资源：某些版本使用 `server.tool(name, description, schema, handler)`（位置参数），其他版本使用 `server.tool({ name, description, inputSchema }, handler)` 或 `registerTool()`。资源也是如此 — 当 API 提供时，在处理程序中包含 `uri`。检查官方 MCP 文档或 Context7 获取当前 `@modelcontextprotocol/sdk` 签名以避免复制粘贴错误。

Use **Zod** (or the SDK's preferred schema format) for input validation.
使用 **Zod**（或 SDK 首选的模式格式）进行输入验证。

## Best Practices

## Best Practices
## 最佳实践

- **Schema first**: Define input schemas for every tool; document parameters and return shape.
  - **模式优先**：为每个工具定义输入模式；记录参数和返回结构。
- **Errors**: Return structured errors or messages the model can interpret; avoid raw stack traces.
  - **错误**：返回模型可以解释的结构化错误或消息；避免原始堆栈跟踪。
- **Idempotency**: Prefer idempotent tools where possible so retries are safe.
  - **幂等性**：尽可能使用幂等工具，以便重试安全。
- **Rate and cost**: For tools that call external APIs, consider rate limits and cost; document in the tool description.
  - **速率和成本**：对于调用外部 API 的工具，考虑速率限制和成本；在工具描述中记录。
- **Versioning**: Pin SDK version in package.json; check release notes when upgrading.
  - **版本控制**：在 package.json 中固定 SDK 版本；升级时检查发布说明。

## Official SDKs and Docs

## Official SDKs and Docs
## 官方 SDK 和文档

- **JavaScript/TypeScript**: `@modelcontextprotocol/sdk` (npm). Use Context7 with library name "MCP" for current registration and transport patterns.
  - **JavaScript/TypeScript**：`@modelcontextprotocol/sdk`（npm）。使用 Context7，库名为"MCP"，获取当前的注册和传输模式。
- **Go**: Official Go SDK on GitHub (`modelcontextprotocol/go-sdk`).
  - **Go**：GitHub 上的官方 Go SDK（`modelcontextprotocol/go-sdk`）。
- **C#**: Official C# SDK for .NET.
  - **C#**：.NET 的官方 C# SDK。
