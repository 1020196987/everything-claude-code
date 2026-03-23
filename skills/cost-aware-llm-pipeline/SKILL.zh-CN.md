---
name: cost-aware-llm-pipeline
description: Cost optimization patterns for LLM API usage — model routing by task complexity, budget tracking, retry logic, and prompt caching.
description zh-CN: LLM API 使用成本优化模式——按任务复杂度进行模型路由、预算跟踪、重试逻辑和提示缓存。
origin: ECC
---

# Cost-Aware LLM Pipeline
# Cost-Aware LLM Pipeline
# 成本感知的 LLM 管道

Patterns for controlling LLM API costs while maintaining quality. Combines model routing, budget tracking, retry logic, and prompt caching into a composable pipeline.
## Patterns for controlling LLM API costs while maintaining quality. Combines model routing, budget tracking, retry logic, and prompt caching into a composable pipeline.
## 在保持质量的同时控制 LLM API 成本的模式。将模型路由、预算跟踪、重试逻辑和提示缓存组合成一个可组合的管道。

## When to Activate
## When to Activate
## 何时激活

- Building applications that call LLM APIs (Claude, GPT, etc.)
  - 构建调用 LLM API 的应用程序（Claude、GPT 等）
- Processing batches of items with varying complexity
  - 处理不同复杂度的批次项目
- Need to stay within a budget for API spend
  - 需要保持在 API 支出预算内
- Optimizing cost without sacrificing quality on complex tasks
  - 在不牺牲复杂任务质量的情况下优化成本

## Core Concepts
## Core Concepts
## 核心概念

### 1. Model Routing by Task Complexity
### 1. Model Routing by Task Complexity
### 1. 按任务复杂度进行模型路由

Automatically select cheaper models for simple tasks, reserving expensive models for complex ones.
## Automatically select cheaper models for simple tasks, reserving expensive models for complex ones.
## 自动为简单任务选择更便宜的模型，为复杂任务保留昂贵的模型。

```python
MODEL_SONNET = "claude-sonnet-4-6"
MODEL_HAIKU = "claude-haiku-4-5-20251001"

_SONNET_TEXT_THRESHOLD = 10_000  # chars
_SONNET_ITEM_THRESHOLD = 30     # items

def select_model(
    text_length: int,
    item_count: int,
    force_model: str | None = None,
) -> str:
    """Select model based on task complexity."""
    if force_model is not None:
        return force_model
    if text_length >= _SONNET_TEXT_THRESHOLD or item_count >= _SONNET_ITEM_THRESHOLD:
        return MODEL_SONNET  # Complex task
    return MODEL_HAIKU  # Simple task (3-4x cheaper)
```

### 2. Immutable Cost Tracking
### 2. Immutable Cost Tracking
### 2. 不可变成本跟踪

Track cumulative spend with frozen dataclasses. Each API call returns a new tracker — never mutates state.
## Track cumulative spend with frozen dataclasses. Each API call returns a new tracker — never mutates state.
## 使用冻结的数据类跟踪累计支出。每次 API 调用返回一个新的跟踪器——从不改变状态。

```python
from dataclasses import dataclass

@dataclass(frozen=True, slots=True)
class CostRecord:
    model: str
    input_tokens: int
    output_tokens: int
    cost_usd: float

@dataclass(frozen=True, slots=True)
class CostTracker:
    budget_limit: float = 1.00
    records: tuple[CostRecord, ...] = ()

    def add(self, record: CostRecord) -> "CostTracker":
        """Return new tracker with added record (never mutates self)."""
        return CostTracker(
            budget_limit=self.budget_limit,
            records=(*self.records, record),
        )

    @property
    def total_cost(self) -> float:
        return sum(r.cost_usd for r in self.records)

    @property
    def over_budget(self) -> bool:
        return self.total_cost > self.budget_limit
```

### 3. Narrow Retry Logic
### 3. Narrow Retry Logic
### 3. 窄化重试逻辑

Retry only on transient errors. Fail fast on authentication or bad request errors.
## Retry only on transient errors. Fail fast on authentication or bad request errors.
## 仅对临时错误重试。对认证或错误请求错误快速失败。

```python
from anthropic import (
    APIConnectionError,
    InternalServerError,
    RateLimitError,
)

_RETRYABLE_ERRORS = (APIConnectionError, RateLimitError, InternalServerError)
_MAX_RETRIES = 3

def call_with_retry(func, *, max_retries: int = _MAX_RETRIES):
    """Retry only on transient errors, fail fast on others."""
    for attempt in range(max_retries):
        try:
            return func()
        except _RETRYABLE_ERRORS:
            if attempt == max_retries - 1:
                raise
            time.sleep(2 ** attempt)  # Exponential backoff
    # AuthenticationError, BadRequestError etc. -> raise immediately
```

### 4. Prompt Caching
### 4. Prompt Caching
### 4. 提示缓存

Cache long system prompts to avoid resending them on every request.
## Cache long system prompts to avoid resending them on every request.
## 缓存长系统提示以避免在每次请求时重新发送。

```python
messages = [
    {
        "role": "user",
        "content": [
            {
                "type": "text",
                "text": system_prompt,
                "cache_control": {"type": "ephemeral"},  # Cache this
            },
            {
                "type": "text",
                "text": user_input,  # Variable part
            },
        ],
    }
]
```

## Composition
## Composition
## 组合

Combine all four techniques in a single pipeline function:
## Combine all four techniques in a single pipeline function.
## 在单个管道函数中组合所有四种技术：

```python
def process(text: str, config: Config, tracker: CostTracker) -> tuple[Result, CostTracker]:
    # 1. Route model
    # 1. 路由模型
    model = select_model(len(text), estimated_items, config.force_model)

    # 2. Check budget
    # 2. 检查预算
    if tracker.over_budget:
        raise BudgetExceededError(tracker.total_cost, tracker.budget_limit)

    # 3. Call with retry + caching
    # 3. 调用重试 + 缓存
    response = call_with_retry(lambda: client.messages.create(
        model=model,
        messages=build_cached_messages(system_prompt, text),
    ))

    # 4. Track cost (immutable)
    # 4. 跟踪成本（不可变）
    record = CostRecord(model=model, input_tokens=..., output_tokens=..., cost_usd=...)
    tracker = tracker.add(record)

    return parse_result(response), tracker
```

## Pricing Reference (2025-2026)
## Pricing Reference (2025-2026)
## 定价参考（2025-2026）

| Model | Input ($/1M tokens) | Output ($/1M tokens) | Relative Cost |
|-------|---------------------|----------------------|---------------|
| Haiku 4.5 | $0.80 | $4.00 | 1x |
| Sonnet 4.6 | $3.00 | $15.00 | ~4x |
| Opus 4.5 | $15.00 | $75.00 | ~19x |

## Best Practices
## Best Practices
## 最佳实践

- **Start with the cheapest model** and only route to expensive models when complexity thresholds are met
  - **从最便宜的模型开始**，仅在达到复杂度阈值时才路由到昂贵的模型
- **Set explicit budget limits** before processing batches — fail early rather than overspend
  - **在处理批次前设置明确的预算限制**——提前失败而不是超支
- **Log model selection decisions** so you can tune thresholds based on real data
  - **记录模型选择决策**，以便根据真实数据调整阈值
- **Use prompt caching** for system prompts over 1024 tokens — saves both cost and latency
  - **使用提示缓存**处理超过 1024 个 token 的系统提示——节省成本和延迟
- **Never retry on authentication or validation errors** — only transient failures (network, rate limit, server error)
  - **永远不要对认证或验证错误重试**——仅对临时失败（网络、速率限制、服务器错误）重试

## Anti-Patterns to Avoid
## Anti-Patterns to Avoid
## 应避免的反模式

- Using the most expensive model for all requests regardless of complexity
  - 无论复杂度如何，对所有请求使用最昂贵的模型
- Retrying on all errors (wastes budget on permanent failures)
  - 对所有错误重试（浪费永久失败的预算）
- Mutating cost tracking state (makes debugging and auditing difficult)
  - 改变成本跟踪状态（使调试和审计困难）
- Hardcoding model names throughout the codebase (use constants or config)
  - 在整个代码库中硬编码模型名称（使用常量或配置）
- Ignoring prompt caching for repetitive system prompts
  - 忽略重复系统提示的提示缓存

## When to Use
## When to Use
## 使用场景

- Any application calling Claude, OpenAI, or similar LLM APIs
  - 任何调用 Claude、OpenAI 或类似 LLM API 的应用程序
- Batch processing pipelines where cost adds up quickly
  - 成本快速累积的批处理管道
- Multi-model architectures that need intelligent routing
  - 需要智能路由的多模型架构
- Production systems that need budget guardrails
  - 需要预算护栏的生产系统
