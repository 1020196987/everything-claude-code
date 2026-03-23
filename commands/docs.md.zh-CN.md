---
description: Look up current documentation for a library or topic via Context7.
description-zh: 通过 Context7 查找库或主题的最新文档。
---

# /docs
# /docs

Look up up-to-date documentation for a library, framework, or API and return a summarized answer with relevant code snippets. Uses the Context7 MCP (resolve-library-id and query-docs) so answers reflect current docs, not training data.
查找库、框架或 API 的最新文档，并返回带有相关代码片段的摘要答案。使用 Context7 MCP（resolve-library-id 和 query-docs），因此答案反映当前文档，而不是训练数据。

## Usage
## 使用方式

```
/docs [library name] [question]
```

Use quotes for multi-word arguments so they are parsed as a single token.
使用引号包裹多词参数，以便解析为单个标记。

## Workflow
## 工作流程

1. **Resolve library ID** - Call the Context7 tool `resolve-library-id` with the library name and the user's question to get a Context7-compatible library ID.
   **解析库 ID** - 使用库名和用户问题调用 Context7 工具 `resolve-library-id`，获取 Context7 兼容的库 ID。
2. **Query docs** - Call `query-docs` with that library ID and the user's question.
   **查询文档** - 使用该库 ID 和用户问题调用 `query-docs`。
3. **Summarize** - Return a concise answer and include relevant code examples from the fetched documentation.
   **总结** - 返回简洁答案，并包含从获取的文档中的相关代码示例。
