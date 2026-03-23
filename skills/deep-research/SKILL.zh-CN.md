---
name: deep-research
description: Multi-source deep research using firecrawl and exa MCPs. Searches the web, synthesizes findings, and delivers cited reports with source attribution. Use when the user wants thorough research on any topic with evidence and citations.
description zh-CN: 使用 firecrawl 和 exa MCP 进行多源深度研究。搜索网络、整合发现，并提供带有来源归属的引用报告。当用户需要对任何主题进行有证据和引用的深入研究时使用。
origin: ECC
---

# Deep Research

## Deep Research
## 深度研究

## When to Activate

## When to Activate
## 何时激活

- User asks to research any topic in depth
  用户要求深入研究任何主题
- Competitive analysis, technology evaluation, or market sizing
  竞争分析、技术评估或市场规模估算
- Due diligence on companies, investors, or technologies
  对公司、投资者或技术进行尽职调查
- Any question requiring synthesis from multiple sources
  需要从多个来源综合的任何问题
- User says "research", "deep dive", "investigate", or "what's the current state of"
  用户说"研究"、"深入探讨"、"调查"或"当前状态是什么"

## MCP Requirements

## MCP Requirements
## MCP 需求

At least one of:
至少需要以下之一：

- **firecrawl** — `firecrawl_search`, `firecrawl_scrape`, `firecrawl_crawl`
- **exa** — `web_search_exa`, `web_search_advanced_exa`, `crawling_exa`

Both together give the best coverage. Configure in `~/.claude.json` or `~/.codex/config.toml`.
两者结合可获得最佳覆盖范围。在 `~/.claude.json` 或 `~/.codex/config.toml` 中配置。

## Workflow

## Workflow
## 工作流程

### Step 1: Understand the Goal

### Step 1: Understand the Goal
### 步骤 1：理解目标

Ask 1-2 quick clarifying questions:
提出 1-2 个快速澄清问题：

- "What's your goal — learning, making a decision, or writing something?"
  "你的目标是什么——学习、做决定，还是写东西？"
- "Any specific angle or depth you want?"
  "你想要特定的视角或深度吗？"

If the user says "just research it" — skip ahead with reasonable defaults.
如果用户说"直接研究吧"——以合理的默认设置继续。

### Step 2: Plan the Research

### Step 2: Plan the Research
### 步骤 2：规划研究

Break the topic into 3-5 research sub-questions. Example:
将主题分解为 3-5 个研究子问题。例如：

- Topic: "Impact of AI on healthcare"
  主题："AI 对医疗保健的影响"
  - What are the main AI applications in healthcare today?
    如今医疗保健领域的主要 AI 应用是什么？
  - What clinical outcomes have been measured?
    已测量了哪些临床结果？
  - What are the regulatory challenges?
    监管挑战是什么？
  - What companies are leading this space?
    哪些公司处于领先地位？
  - What's the market size and growth trajectory?
    市场规模和增长轨迹是什么？

### Step 3: Execute Multi-Source Search

### Step 3: Execute Multi-Source Search
### 步骤 3：执行多源搜索

For EACH sub-question, search using available MCP tools:
对每个子问题，使用可用的 MCP 工具进行搜索：

**With firecrawl:**
**使用 firecrawl：**
```
firecrawl_search(query: "<sub-question keywords>", limit: 8)
```

**With exa:**
**使用 exa：**
```
web_search_exa(query: "<sub-question keywords>", numResults: 8)
web_search_advanced_exa(query: "<keywords>", numResults: 5, startPublishedDate: "2025-01-01")
```

**Search strategy:**
**搜索策略：**
- Use 2-3 different keyword variations per sub-question
  每个子问题使用 2-3 个不同的关键词变体
- Mix general and news-focused queries
  混合通用和新闻聚焦的查询
- Aim for 15-30 unique sources total
  目标是总共 15-30 个独特来源
- Prioritize: academic, official, reputable news > blogs > forums
  优先级：学术、官方、知名新闻 > 博客 > 论坛

### Step 4: Deep-Read Key Sources

### Step 4: Deep-Read Key Sources
### 步骤 4：深度阅读关键来源

For the most promising URLs, fetch full content:
对于最有希望的 URL，获取完整内容：

**With firecrawl:**
**使用 firecrawl：**
```
firecrawl_scrape(url: "<url>")
```

**With exa:**
**使用 exa：**
```
crawling_exa(url: "<url>", tokensNum: 5000)
```

Read 3-5 key sources in full for depth. Do not rely only on search snippets.
深度阅读 3-5 个关键来源的全文。不要仅依赖搜索摘要。

### Step 5: Synthesize and Write Report

### Step 5: Synthesize and Write Report
### 步骤 5：综合并撰写报告

Structure the report:
构建报告结构：

```markdown
# [Topic]: Research Report
*Generated: [date] | Sources: [N] | Confidence: [High/Medium/Low]*

## Executive Summary
[3-5 sentence overview of key findings]

## 1. [First Major Theme]
[Findings with inline citations]
- Key point ([Source Name](url))
- Supporting data ([Source Name](url))

## 2. [Second Major Theme]
...

## 3. [Third Major Theme]
...

## Key Takeaways
- [Actionable insight 1]
- [Actionable insight 2]
- [Actionable insight 3]

## Sources
1. [Title](url) — [one-line summary]
2. ...

## Methodology
Searched [N] queries across web and news. Analyzed [M] sources.
Sub-questions investigated: [list]
```

### Step 6: Deliver

### Step 6: Deliver
### 步骤 6：交付

- **Short topics**: Post the full report in chat
  简短主题：在聊天中发布完整报告
- **Long reports**: Post the executive summary + key takeaways, save full report to a file
  长报告：发布执行摘要 + 关键要点，将完整报告保存到文件

## Parallel Research with Subagents

## Parallel Research with Subagents
## 使用子代理进行并行研究

For broad topics, use Claude Code's Task tool to parallelize:
对于广泛的主题，使用 Claude Code 的 Task 工具进行并行化：

```
Launch 3 research agents in parallel:
启动 3 个研究代理并行工作：
1. Agent 1: Research sub-questions 1-2
   代理 1：研究子问题 1-2
2. Agent 2: Research sub-questions 3-4
   代理 2：研究子问题 3-4
3. Agent 3: Research sub-question 5 + cross-cutting themes
   代理 3：研究子问题 5 + 跨领域主题
```

Each agent searches, reads sources, and returns findings. The main session synthesizes into the final report.
每个代理搜索、阅读来源并返回发现。主要会话综合成最终报告。

## Quality Rules

## Quality Rules
## 质量规则

1. **Every claim needs a source.** No unsourced assertions.
   每个声明都需要来源。没有无来源的断言。
2. **Cross-reference.** If only one source says it, flag it as unverified.
   交叉引用。如果只有一个来源这样说，则标记为未验证。
3. **Recency matters.** Prefer sources from the last 12 months.
   时效性很重要。优先选择过去 12 个月的来源。
4. **Acknowledge gaps.** If you couldn't find good info on a sub-question, say so.
   承认空白。如果无法找到关于子问题的好信息，请说明。
5. **No hallucination.** If you don't know, say "insufficient data found."
   不要编造。如果不知道，说"未找到足够数据"。
6. **Separate fact from inference.** Label estimates, projections, and opinions clearly.
   将事实与推断分开。清楚标注估计、预测和观点。

## Examples

## Examples
## 示例

```
"Research the current state of nuclear fusion energy"
"研究核聚变能源的当前状态"
"Deep dive into Rust vs Go for backend services in 2026"
"2026 年 Rust 与 Go 后端服务的深入对比"
"Research the best strategies for bootstrapping a SaaS business"
"研究创业 SaaS 业务的最佳策略"
"What's happening with the US housing market right now?"
"美国房地产市场现在的情况如何？"
"Investigate the competitive landscape for AI code editors"
"调查 AI 代码编辑器的竞争格局"
```
