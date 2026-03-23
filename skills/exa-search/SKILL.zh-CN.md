---
name: exa-search
description: Neural search via Exa MCP for web, code, and company research. Use when the user needs web search, code examples, company intel, people lookup, or AI-powered deep research with Exa's neural search engine.
description zh-CN: 通过Exa MCP进行神经网络搜索，用于网络、代码和公司研究。当用户需要网络搜索、代码示例、公司情报、人员查询或使用Exa神经网络搜索引擎进行AI驱动的深度研究时使用。
origin: ECC
---

# Exa Search

## Exa Search
## Exa 搜索

Neural search for web content, code, companies, and people via the Exa MCP server.
通过Exa MCP服务器进行网络内容、代码、公司和人员的神经网络搜索。

## When to Activate

## When to Activate
## 何时激活

- User needs current web information or news
  用户需要当前网络信息或新闻
- Searching for code examples, API docs, or technical references
  搜索代码示例、API文档或技术参考
- Researching companies, competitors, or market players
  研究公司、竞争对手或市场参与者
- Finding professional profiles or people in a domain
  寻找某个领域的专业资料或人员
- Running background research for any development task
  为任何开发任务运行背景研究
- User says "search for", "look up", "find", or "what's the latest on"
  用户说"搜索"、"查找"、"寻找"或"最近有什么关于...的消息"

## MCP Requirement

## MCP Requirement
## MCP要求

Exa MCP server must be configured. Add to `~/.claude.json`:
必须配置Exa MCP服务器。添加到`~/.claude.json`：

```json
"exa-web-search": {
  "command": "npx",
  "args": ["-y", "exa-mcp-server"],
  "env": { "EXA_API_KEY": "YOUR_EXA_API_KEY_HERE" }
}
```

Get an API key at [exa.ai](https://exa.ai).
在[exa.ai](https://exa.ai)获取API密钥。

This repo's current Exa setup documents the tool surface exposed here: `web_search_exa` and `get_code_context_exa`.
此仓库当前的Exa设置记录了此处公开的工具接口：`web_search_exa`和`get_code_context_exa`。

If your Exa server exposes additional tools, verify their exact names before depending on them in docs or prompts.
如果您的Exa服务器公开了其他工具，在文档或提示中依赖之前请验证其确切名称。

## Core Tools

## Core Tools
## 核心工具

### web_search_exa

General web search for current information, news, or facts.
用于当前信息、新闻或事实的通用网络搜索。

```
web_search_exa(query: "latest AI developments 2026", numResults: 5)
```

**Parameters:**

**Parameters:**
**参数：**

| Param | Type | Default | Notes |
|-------|------|---------|-------|
| Param | Type | Default | Notes |
| 参数 | 类型 | 默认值 | 说明 |
| `query` | string | required | Search query |
| `query` | string | 必需 | 搜索查询 |
| `numResults` | number | 8 | Number of results |
| `numResults` | number | 8 | 结果数量 |
| `type` | string | `auto` | Search mode |
| `type` | string | `auto` | 搜索模式 |
| `livecrawl` | string | `fallback` | Prefer live crawling when needed |
| `livecrawl` | string | `fallback` | 需要时优先实时爬取 |
| `category` | string | none | Optional focus such as `company` or `research paper` |
| `category` | string | 无 | 可选聚焦，如`company`或`research paper` |

### get_code_context_exa

Find code examples and documentation from GitHub, Stack Overflow, and docs sites.
从GitHub、Stack Overflow和文档网站查找代码示例和文档。

```
get_code_context_exa(query: "Python asyncio patterns", tokensNum: 3000)
```

**Parameters:**

**Parameters:**
**参数：**

| Param | Type | Default | Notes |
|-------|------|---------|-------|
| Param | Type | Default | Notes |
| 参数 | 类型 | 默认值 | 说明 |
| `query` | string | required | Code or API search query |
| `query` | string | 必需 | 代码或API搜索查询 |
| `tokensNum` | number | 5000 | Content tokens (1000-50000) |
| `tokensNum` | number | 5000 | 内容token（1000-50000） |

## Usage Patterns

## Usage Patterns
## 使用模式

### Quick Lookup

### Quick Lookup
### 快速查询

```
web_search_exa(query: "Node.js 22 new features", numResults: 3)
```

### Code Research

### Code Research
### 代码研究

```
get_code_context_exa(query: "Rust error handling patterns Result type", tokensNum: 3000)
```

### Company or People Research

### Company or People Research
### 公司或人员研究

```
web_search_exa(query: "Vercel funding valuation 2026", numResults: 3, category: "company")
web_search_exa(query: "site:linkedin.com/in AI safety researchers Anthropic", numResults: 5)
```

### Technical Deep Dive

### Technical Deep Dive
### 技术深度探讨

```
web_search_exa(query: "WebAssembly component model status and adoption", numResults: 5)
get_code_context_exa(query: "WebAssembly component model examples", tokensNum: 4000)
```

## Tips

## Tips
## 提示

- Use `web_search_exa` for current information, company lookups, and broad discovery
  使用`web_search_exa`获取当前信息、公司查询和广泛发现
- Use search operators like `site:`, quoted phrases, and `intitle:` to narrow results
  使用搜索运算符如`site:`、引号短语和`intitle:`来缩小结果范围
- Lower `tokensNum` (1000-2000) for focused code snippets, higher (5000+) for comprehensive context
  较低的`tokensNum`（1000-2000）用于聚焦的代码片段，较高的（5000+）用于全面的上下文
- Use `get_code_context_exa` when you need API usage or code examples rather than general web pages
  当您需要API用法或代码示例而非一般网页时使用`get_code_context_exa`

## Related Skills

## Related Skills
## 相关技能

- `deep-research` — Full research workflow using firecrawl + exa together
  `deep-research` — 使用firecrawl + exa的完整研究工作流
- `market-research` — Business-oriented research with decision frameworks
  `market-research` — 带决策框架的商业导向研究
