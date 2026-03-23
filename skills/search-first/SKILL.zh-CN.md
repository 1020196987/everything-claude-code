---
name: search-first
description: Research-before-coding workflow. Search for existing tools, libraries, and patterns before writing custom code. Invokes the researcher agent.
description zh-CN: 研究优先编码工作流。在编写自定义代码之前搜索现有工具、库和模式。调用研究agent。
origin: ECC
---

# /search-first — Research Before You Code

## English

Systematizes the "search for existing solutions before implementing" workflow.

## 中文

将"在实施之前搜索现有解决方案"的工作流系统化。

## Trigger

## English

Use this skill when:
- Starting a new feature that likely has existing solutions
- Adding a dependency or integration
- The user asks "add X functionality" and you're about to write code
- Before creating a new utility, helper, or abstraction

## 中文

在以下情况下使用此skill：
- 开始一个可能有现有解决方案的新功能
- 添加依赖项或集成
- 用户要求"添加X功能"而你正要去写代码
- 在创建新的工具、助手或抽象之前

## Workflow

## English

## 中文

工作流

```
┌─────────────────────────────────────────────┐
│  1. NEED ANALYSIS                           │
│     Define what functionality is needed      │
│     Identify language/framework constraints  │
├─────────────────────────────────────────────┤
│  2. PARALLEL SEARCH (researcher agent)      │
│     ┌──────────┐ ┌──────────┐ ┌──────────┐  │
│     │  npm /   │ │  MCP /   │ │  GitHub / │  │
│     │  PyPI    │ │  Skills  │ │  Web      │  │
│     └──────────┘ └──────────┘ └──────────┘  │
├─────────────────────────────────────────────┤
│  3. EVALUATE                                │
│     Score candidates (functionality, maint, │
│     community, docs, license, deps)         │
├─────────────────────────────────────────────┤
│  4. DECIDE                                  │
│     ┌─────────┐  ┌──────────┐  ┌─────────┐  │
│     │  Adopt  │  │  Extend  │  │  Build   │  │
│     │ as-is   │  │  /Wrap   │  │  Custom  │  │
│     └─────────┘  └──────────┘  └─────────┘  │
├─────────────────────────────────────────────┤
│  5. IMPLEMENT                               │
│     Install package / Configure MCP /       │
│     Write minimal custom code               │
└─────────────────────────────────────────────┘
```

## Decision Matrix

## English

## 中文

### 决策矩阵

| Signal | Action |
|--------|--------|
| Exact match, well-maintained, MIT/Apache | **Adopt** — install and use directly |
| Partial match, good foundation | **Extend** — install + write thin wrapper |
| Multiple weak matches | **Compose** — combine 2-3 small packages |
| Nothing suitable found | **Build** — write custom, but informed by research |

| 信号 | 行动 |
|--------|--------|
| 精确匹配，维护良好，MIT/Apache | **采用** — 直接安装使用 |
| 部分匹配，基础良好 | **扩展** — 安装+写薄包装器 |
| 多个弱匹配 | **组合** — 组合2-3个小包 |
| 未找到合适的 | **构建** — 自定义编写，但基于研究 |

## How to Use

## English

### Quick Mode (inline)

Before writing a utility or adding functionality, mentally run through:

0. Does this already exist in the repo? → `rg` through relevant modules/tests first
1. Is this a common problem? → Search npm/PyPI
2. Is there an MCP for this? → Check `~/.claude/settings.json` and search
3. Is there a skill for this? → Check `~/.claude/skills/`
4. Is there a GitHub implementation/template? → Run GitHub code search for maintained OSS before writing net-new code

### Full Mode (agent)

For non-trivial functionality, launch the researcher agent:

## 中文

### 使用方法

#### 快速模式（内联）

在编写工具或添加功能之前，在脑海中过一遍：

0. 这是否已存在于仓库中？→ 首先通过相关模块/测试 `rg`
1. 这是常见问题吗？→ 搜索npm/PyPI
2. 有MCP吗？→ 检查 `~/.claude/settings.json` 并搜索
3. 有skill吗？→ 检查 `~/.claude/skills/`
4. 有GitHub实现/模板吗？→ 在编写全新代码之前运行GitHub代码搜索寻找维护良好的开源软件

#### 完整模式（agent）

对于复杂的功能，启动researcher agent：

```
Task(subagent_type="general-purpose", prompt="
  Research existing tools for: [DESCRIPTION]
  Language/framework: [LANG]
  Constraints: [ANY]

  Search: npm/PyPI, MCP servers, Claude Code skills, GitHub
  Return: Structured comparison with recommendation
")
```

## Search Shortcuts by Category

## English

### Development Tooling
- Linting → `eslint`, `ruff`, `textlint`, `markdownlint`
- Formatting → `prettier`, `black`, `gofmt`
- Testing → `jest`, `pytest`, `go test`
- Pre-commit → `husky`, `lint-staged`, `pre-commit`

### AI/LLM Integration
- Claude SDK → Context7 for latest docs
- Prompt management → Check MCP servers
- Document processing → `unstructured`, `pdfplumber`, `mammoth`

### Data & APIs
- HTTP clients → `httpx` (Python), `ky`/`got` (Node)
- Validation → `zod` (TS), `pydantic` (Python)
- Database → Check for MCP servers first

### Content & Publishing
- Markdown processing → `remark`, `unified`, `markdown-it`
- Image optimization → `sharp`, `imagemin`

## 中文

### 按类别搜索快捷方式

#### 开发工具
- Linting → `eslint`, `ruff`, `textlint`, `markdownlint`
- 格式化 → `prettier`, `black`, `gofmt`
- 测试 → `jest`, `pytest`, `go test`
- 预提交 → `husky`, `lint-staged`, `pre-commit`

#### AI/LLM集成
- Claude SDK → Context7获取最新文档
- 提示管理 → 检查MCP服务器
- 文档处理 → `unstructured`, `pdfplumber`, `mammoth`

#### 数据与API
- HTTP客户端 → `httpx` (Python), `ky`/`got` (Node)
- 验证 → `zod` (TS), `pydantic` (Python)
- 数据库 → 首先检查MCP服务器

#### 内容与发布
- Markdown处理 → `remark`, `unified`, `markdown-it`
- 图像优化 → `sharp`, `imagemin`

## Integration Points

## English

### With planner agent
The planner should invoke researcher before Phase 1 (Architecture Review):
- Researcher identifies available tools
- Planner incorporates them into the implementation plan
- Avoids "reinventing the wheel" in the plan

### With architect agent
The architect should consult researcher for:
- Technology stack decisions
- Integration pattern discovery
- Existing reference architectures

### With iterative-retrieval skill
Combine for progressive discovery:
- Cycle 1: Broad search (npm, PyPI, MCP)
- Cycle 2: Evaluate top candidates in detail
- Cycle 3: Test compatibility with project constraints

## 中文

### 集成点

#### 与planner agent配合
planner应在阶段1（架构审查）之前调用researcher：
- Researcher识别可用工具
- Planner将其纳入实施计划
- 避免计划中的"重复造轮子"

#### 与architect agent配合
architect应为以下方面咨询researcher：
- 技术栈决策
- 集成模式发现
- 现有参考架构

#### 与iterative-retrieval skill配合
结合进行渐进式发现：
- 循环1：广泛搜索（npm, PyPI, MCP）
- 循环2：详细评估顶级候选
- 循环3：测试与项目约束的兼容性

## Examples

## English

### Example 1: "Add dead link checking"
```
Need: Check markdown files for broken links
Search: npm "markdown dead link checker"
Found: textlint-rule-no-dead-link (score: 9/10)
Action: ADOPT — npm install textlint-rule-no-dead-link
Result: Zero custom code, battle-tested solution
```

### Example 2: "Add HTTP client wrapper"
```
Need: Resilient HTTP client with retries and timeout handling
Search: npm "http client retry", PyPI "httpx retry"
Found: got (Node) with retry plugin, httpx (Python) with built-in retry
Action: ADOPT — use got/httpx directly with retry config
Result: Zero custom code, production-proven libraries
```

### Example 3: "Add config file linter"
```
Need: Validate project config files against a schema
Search: npm "config linter schema", "json schema validator cli"
Found: ajv-cli (score: 8/10)
Action: ADOPT + EXTEND — install ajv-cli, write project-specific schema
Result: 1 package + 1 schema file, no custom validation logic
```

## 中文

### 示例

#### 示例1："添加死链检查"
```
需求：检查markdown文件中的损坏链接
搜索：npm "markdown dead link checker"
发现：textlint-rule-no-dead-link（评分：9/10）
行动：采用 — npm install textlint-rule-no-dead-link
结果：零自定义代码，经过实战验证的解决方案
```

#### 示例2："添加HTTP客户端包装器"
```
需求：带重试和超时处理的弹性HTTP客户端
搜索：npm "http client retry", PyPI "httpx retry"
发现：got (Node) 带重试插件，httpx (Python) 内置重试
行动：采用 — 直接使用got/httpx并配置重试
结果：零自定义代码，生产级验证的库
```

#### 示例3："添加配置文件检查器"
```
需求：根据schema验证项目配置文件
搜索：npm "config linter schema", "json schema validator cli"
发现：ajv-cli（评分：8/10）
行动：采用+扩展 — 安装ajv-cli，编写项目特定schema
结果：1个包+1个schema文件，无自定义验证逻辑
```

## Anti-Patterns

## English

- **Jumping to code**: Writing a utility without checking if one exists
  **忽略搜索**：不检查是否存在就编写工具
- **Ignoring MCP**: Not checking if an MCP server already provides the capability
  **忽视MCP**：不检查MCP服务器是否已提供该功能
- **Over-customizing**: Wrapping a library so heavily it loses its benefits
  **过度定制**：过度包装库使其失去优势
- **Dependency bloat**: Installing a massive package for one small feature
  **依赖膨胀**：为一个小功能安装一个巨大的包

## 中文

### 反模式
