---
name: iterative-retrieval
description: Pattern for progressively refining context retrieval to solve the subagent context problem
description zh-CN: 一种渐进式细化上下文检索的模式，用于解决子代理上下文问题
origin: ECC
---

# Iterative Retrieval Pattern
# 迭代检索模式

Solves the "context problem" in multi-agent workflows where subagents don't know what context they need until they start working.
解决多代理工作流中的"上下文问题"，即子代理在开始工作前不知道他们需要什么上下文。

## When to Activate
## 何时激活

- Spawning subagents that need codebase context they cannot predict upfront
  - 生成需要代码库上下文但无法提前预测的子代理
- Building multi-agent workflows where context is progressively refined
  - 构建上下文渐进式细化的多代理工作流
- Encountering "context too large" or "missing context" failures in agent tasks
  - 在代理任务中遇到"上下文太大"或"缺少上下文"失败
- Designing RAG-like retrieval pipelines for code exploration
  - 为代码探索设计类 RAG 检索管道
- Optimizing token usage in agent orchestration
  - 在代理编排中优化 token 使用

## The Problem
## 问题

Subagents are spawned with limited context. They don't know:
子代理在有限的上下文中生成。他们不知道：
- Which files contain relevant code
  - 哪些文件包含相关代码
- What patterns exist in the codebase
  - 代码库中存在哪些模式
- What terminology the project uses
  - 项目使用什么术语

Standard approaches fail:
标准方法失败：
- **Send everything**: Exceeds context limits
  - **发送所有内容**：超出上下文限制
- **Send nothing**: Agent lacks critical information
  - **什么都不发**：代理缺少关键信息
- **Guess what's needed**: Often wrong
  - **猜测需要什么**：经常错误

## The Solution: Iterative Retrieval
## 解决方案：迭代检索

A 4-phase loop that progressively refines context:
一个 4 阶段循环，渐进式细化上下文：

```
┌─────────────────────────────────────────────┐
│                                             │
│   ┌──────────┐      ┌──────────┐            │
│   │ DISPATCH │─────▶│ EVALUATE │            │
│   └──────────┘      └──────────┘            │
│        ▲                  │                 │
│        │                  ▼                 │
│   ┌──────────┐      ┌──────────┐            │
│   │   LOOP   │◀─────│  REFINE  │            │
│   └──────────┘      └──────────┘            │
│                                             │
│        Max 3 cycles, then proceed           │
└─────────────────────────────────────────────┘
```

### Phase 1: DISPATCH
### 阶段 1：分发

Initial broad query to gather candidate files:
初始广泛查询以收集候选文件：

```javascript
// Start with high-level intent
const initialQuery = {
  patterns: ['src/**/*.ts', 'lib/**/*.ts'],
  keywords: ['authentication', 'user', 'session'],
  excludes: ['*.test.ts', '*.spec.ts']
};

// Dispatch to retrieval agent
const candidates = await retrieveFiles(initialQuery);
```

### Phase 2: EVALUATE
### 阶段 2：评估

Assess retrieved content for relevance:
评估检索内容的相关性：

```javascript
function evaluateRelevance(files, task) {
  return files.map(file => ({
    path: file.path,
    relevance: scoreRelevance(file.content, task),
    reason: explainRelevance(file.content, task),
    missingContext: identifyGaps(file.content, task)
  }));
}
```

Scoring criteria:
评分标准：
- **High (0.8-1.0)**: Directly implements target functionality
  - **高（0.8-1.0）**：直接实现目标功能
- **Medium (0.5-0.7)**: Contains related patterns or types
  - **中（0.5-0.7）**：包含相关模式或类型
- **Low (0.2-0.4)**: Tangentially related
  - **低（0.2-0.4）**：边缘相关
- **None (0-0.2)**: Not relevant, exclude
  - **无（0-0.2）**：不相关，排除

### Phase 3: REFINE
### 阶段 3：细化

Update search criteria based on evaluation:
根据评估更新搜索标准：

```javascript
function refineQuery(evaluation, previousQuery) {
  return {
    // Add new patterns discovered in high-relevance files
    patterns: [...previousQuery.patterns, ...extractPatterns(evaluation)],

    // Add terminology found in codebase
    keywords: [...previousQuery.keywords, ...extractKeywords(evaluation)],

    // Exclude confirmed irrelevant paths
    excludes: [...previousQuery.excludes, ...evaluation
      .filter(e => e.relevance < 0.2)
      .map(e => e.path)
    ],

    // Target specific gaps
    focusAreas: evaluation
      .flatMap(e => e.missingContext)
      .filter(unique)
  };
}
```

### Phase 4: LOOP
### 阶段 4：循环

Repeat with refined criteria (max 3 cycles):
用细化的标准重复（最多 3 个周期）：

```javascript
async function iterativeRetrieve(task, maxCycles = 3) {
  let query = createInitialQuery(task);
  let bestContext = [];

  for (let cycle = 0; cycle < maxCycles; cycle++) {
    const candidates = await retrieveFiles(query);
    const evaluation = evaluateRelevance(candidates, task);

    // Check if we have sufficient context
    const highRelevance = evaluation.filter(e => e.relevance >= 0.7);
    if (highRelevance.length >= 3 && !hasCriticalGaps(evaluation)) {
      return highRelevance;
    }

    // Refine and continue
    query = refineQuery(evaluation, query);
    bestContext = mergeContext(bestContext, highRelevance);
  }

  return bestContext;
}
```

## Practical Examples
## 实践示例

### Example 1: Bug Fix Context
### 示例 1：Bug 修复上下文

```
Task: "Fix the authentication token expiry bug"

Cycle 1:
  DISPATCH: Search for "token", "auth", "expiry" in src/**
  EVALUATE: Found auth.ts (0.9), tokens.ts (0.8), user.ts (0.3)
  REFINE: Add "refresh", "jwt" keywords; exclude user.ts

Cycle 2:
  DISPATCH: Search refined terms
  EVALUATE: Found session-manager.ts (0.95), jwt-utils.ts (0.85)
  REFINE: Sufficient context (2 high-relevance files)

Result: auth.ts, tokens.ts, session-manager.ts, jwt-utils.ts
```

任务："修复认证令牌过期 bug"

周期 1：
  分发：在 src/** 中搜索"token"、"auth"、"expiry"
  评估：找到 auth.ts (0.9)、tokens.ts (0.8)、user.ts (0.3)
  细化：添加"refresh"、"jwt"关键词；排除 user.ts

周期 2：
  分发：搜索细化后的术语
  评估：找到 session-manager.ts (0.95)、jwt-utils.ts (0.85)
  细化：上下文充足（2 个高相关文件）

结果：auth.ts、tokens.ts、session-manager.ts、jwt-utils.ts

### Example 2: Feature Implementation
### 示例 2：功能实现

```
Task: "Add rate limiting to API endpoints"

Cycle 1:
  DISPATCH: Search "rate", "limit", "api" in routes/**
  EVALUATE: No matches - codebase uses "throttle" terminology
  REFINE: Add "throttle", "middleware" keywords

Cycle 2:
  DISPATCH: Search refined terms
  EVALUATE: Found throttle.ts (0.9), middleware/index.ts (0.7)
  REFINE: Need router patterns

Cycle 3:
  DISPATCH: Search "router", "express" patterns
  EVALUATE: Found router-setup.ts (0.8)
  REFINE: Sufficient context

Result: throttle.ts, middleware/index.ts, router-setup.ts
```

任务："为 API 端点添加限流"

周期 1：
  分发：在 routes/** 中搜索"rate"、"limit"、"api"
  评估：无匹配——代码库使用"throttle"术语
  细化：添加"throttle"、"middleware"关键词

周期 2：
  分发：搜索细化后的术语
  评估：找到 throttle.ts (0.9)、middleware/index.ts (0.7)
  细化：需要路由器模式

周期 3：
  分发：搜索"router"、"express"模式
  评估：找到 router-setup.ts (0.8)
  细化：上下文充足

结果：throttle.ts、middleware/index.ts、router-setup.ts

## Integration with Agents
## 与代理的集成

Use in agent prompts:
在代理提示中使用：

```markdown
When retrieving context for this task:
1. Start with broad keyword search
2. Evaluate each file's relevance (0-1 scale)
3. Identify what context is still missing
4. Refine search criteria and repeat (max 3 cycles)
5. Return files with relevance >= 0.7
```

在为此任务检索上下文时：
1. 从广泛的关键词搜索开始
2. 评估每个文件的相关性（0-1 评分）
3. 识别仍然缺少的上下文
4. 细化搜索标准并重复（最多 3 个周期）
5. 返回相关性 >= 0.7 的文件

## Best Practices
## 最佳实践

1. **Start broad, narrow progressively** - Don't over-specify initial queries
   - **从宽泛开始，逐步收窄** - 不要过度指定初始查询
2. **Learn codebase terminology** - First cycle often reveals naming conventions
   - **学习代码库术语** - 第一周期通常揭示命名约定
3. **Track what's missing** - Explicit gap identification drives refinement
   - **跟踪缺失的内容** - 明确的差距识别推动细化
4. **Stop at "good enough"** - 3 high-relevance files beats 10 mediocre ones
   - **在"足够好"时停止** - 3 个高相关文件胜过 10 个平庸文件
5. **Exclude confidently** - Low-relevance files won't become relevant
   - **自信地排除** - 低相关文件不会变得相关

## Related
## 相关内容

- [The Longform Guide](https://x.com/affaanmustafa/status/2014040193557471352) - Subagent orchestration section
  - [The Longform Guide](https://x.com/affaanmustafa/status/2014040193557471352) - 子代理编排部分
- `continuous-learning` skill - For patterns that improve over time
  - `continuous-learning` 技能 - 用于随时间改进的模式
- Agent definitions bundled with ECC (manual install path: `agents/`)
  - ECC 捆绑的代理定义（手动安装路径：`agents/`）
