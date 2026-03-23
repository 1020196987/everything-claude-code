---
name: claude-api
description: Anthropic Claude API patterns for Python and TypeScript. Covers Messages API, streaming, tool use, vision, extended thinking, batches, prompt caching, and Claude Agent SDK. Use when building applications with the Claude API or Anthropic SDKs.
description zh-CN: Anthropic Claude API 模式，涵盖 Python 和 TypeScript。包括 Messages API、流式输出、工具调用、视觉识别、扩展思考、批处理、提示缓存和 Claude Agent SDK。在使用 Claude API 或 Anthropic SDK 构建应用时使用。
origin: ECC
---

# Claude API
# Claude API

Build applications with the Anthropic Claude API and SDKs.
使用 Anthropic Claude API 和 SDK 构建应用。

## When to Activate
## 何时激活

- Building applications that call the Claude API
  - 构建调用 Claude API 的应用
- Code imports `anthropic` (Python) or `@anthropic-ai/sdk` (TypeScript)
  - 代码导入 `anthropic`（Python）或 `@anthropic-ai/sdk`（TypeScript）
- User asks about Claude API patterns, tool use, streaming, or vision
  - 用户询问 Claude API 模式、工具调用、流式输出或视觉识别
- Implementing agent workflows with Claude Agent SDK
  - 使用 Claude Agent SDK 实现智能体工作流
- Optimizing API costs, token usage, or latency
  - 优化 API 成本、令牌使用或延迟

## Model Selection
## 模型选择

| Model | ID | Best For |
| 模型 | ID | 最适合 |
|-------|-----|----------|
| Opus 4.1 | `claude-opus-4-1` | Complex reasoning, architecture, research |
| Opus 4.1 | `claude-opus-4-1` | 复杂推理、架构、研究 |
| Sonnet 4 | `claude-sonnet-4-0` | Balanced coding, most development tasks |
| Sonnet 4 | `claude-sonnet-4-0` | 平衡编码、大多数开发任务 |
| Haiku 3.5 | `claude-3-5-haiku-latest` | Fast responses, high-volume, cost-sensitive |
| Haiku 3.5 | `claude-3-5-haiku-latest` | 快速响应、高容量、成本敏感 |

Default to Sonnet 4 unless the task requires deep reasoning (Opus) or speed/cost optimization (Haiku). For production, prefer pinned snapshot IDs over aliases.
除非任务需要深度推理（Opus）或速度/成本优化（Haiku），否则默认使用 Sonnet 4。对于生产环境，优先使用固定快照 ID 而非别名。

## Python SDK
## Python SDK

### Installation
### 安装

```bash
pip install anthropic
```

### Basic Message
### 基本消息

```python
import anthropic

client = anthropic.Anthropic()  # reads ANTHROPIC_API_KEY from env
# 从环境变量读取 ANTHROPIC_API_KEY

message = client.messages.create(
    model="claude-sonnet-4-0",
    max_tokens=1024,
    messages=[
        {"role": "user", "content": "Explain async/await in Python"}
    ]
)
print(message.content[0].text)
```

### Streaming
### 流式输出

```python
with client.messages.stream(
    model="claude-sonnet-4-0",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Write a haiku about coding"}]
) as stream:
    for text in stream.text_stream:
        print(text, end="", flush=True)
```

### System Prompt
### 系统提示

```python
message = client.messages.create(
    model="claude-sonnet-4-0",
    max_tokens=1024,
    system="You are a senior Python developer. Be concise.",
    messages=[{"role": "user", "content": "Review this function"}]
)
```

## TypeScript SDK
## TypeScript SDK

### Installation
### 安装

```bash
npm install @anthropic-ai/sdk
```

### Basic Message
### 基本消息

```typescript
import Anthropic from "@anthropic-ai/sdk";

const client = new Anthropic(); // reads ANTHROPIC_API_KEY from env
// 从环境变量读取 ANTHROPIC_API_KEY

const message = await client.messages.create({
  model: "claude-sonnet-4-0",
  max_tokens: 1024,
  messages: [
    { role: "user", content: "Explain async/await in TypeScript" }
  ],
});
console.log(message.content[0].text);
```

### Streaming
### 流式输出

```typescript
const stream = client.messages.stream({
  model: "claude-sonnet-4-0",
  max_tokens: 1024,
  messages: [{ role: "user", content: "Write a haiku" }],
});

for await (const event of stream) {
  if (event.type === "content_block_delta" && event.delta.type === "text_delta") {
    process.stdout.write(event.delta.text);
  }
}
```

## Tool Use
## 工具调用

Define tools and let Claude call them:
定义工具并让 Claude 调用它们：

```python
tools = [
    {
        "name": "get_weather",
        "description": "Get current weather for a location",
        "input_schema": {
            "type": "object",
            "properties": {
                "location": {"type": "string", "description": "City name"},
                "unit": {"type": "string", "enum": ["celsius", "fahrenheit"]}
            },
            "required": ["location"]
        }
    }
]

message = client.messages.create(
    model="claude-sonnet-4-0",
    max_tokens=1024,
    tools=tools,
    messages=[{"role": "user", "content": "What's the weather in SF?"}]
)

# Handle tool use response
# 处理工具调用响应
for block in message.content:
    if block.type == "tool_use":
        # Execute the tool with block.input
        # 使用 block.input 执行工具
        result = get_weather(**block.input)
        # Send result back
        # 发送结果返回
        follow_up = client.messages.create(
            model="claude-sonnet-4-0",
            max_tokens=1024,
            tools=tools,
            messages=[
                {"role": "user", "content": "What's the weather in SF?"},
                {"role": "assistant", "content": message.content},
                {"role": "user", "content": [
                    {"type": "tool_result", "tool_use_id": block.id, "content": str(result)}
                ]}
            ]
        )
```

## Vision
## 视觉识别

Send images for analysis:
发送图像进行分析：

```python
import base64

with open("diagram.png", "rb") as f:
    image_data = base64.standard_b64encode(f.read()).decode("utf-8")

message = client.messages.create(
    model="claude-sonnet-4-0",
    max_tokens=1024,
    messages=[{
        "role": "user",
        "content": [
            {"type": "image", "source": {"type": "base64", "media_type": "image/png", "data": image_data}},
            {"type": "text", "text": "Describe this diagram"}
        ]
    }]
)
```

## Extended Thinking
## 扩展思考

For complex reasoning tasks:
用于复杂推理任务：

```python
message = client.messages.create(
    model="claude-sonnet-4-0",
    max_tokens=16000,
    thinking={
        "type": "enabled",
        "budget_tokens": 10000
    },
    messages=[{"role": "user", "content": "Solve this math problem step by step..."}]
)

for block in message.content:
    if block.type == "thinking":
        print(f"Thinking: {block.thinking}")
    elif block.type == "text":
        print(f"Answer: {block.text}")
```

## Prompt Caching
## 提示缓存

Cache large system prompts or context to reduce costs:
缓存大型系统提示或上下文以降低成本：

```python
message = client.messages.create(
    model="claude-sonnet-4-0",
    max_tokens=1024,
    system=[
        {"type": "text", "text": large_system_prompt, "cache_control": {"type": "ephemeral"}}
    ],
    messages=[{"role": "user", "content": "Question about the cached context"}]
)
# Check cache usage
# 检查缓存使用
print(f"Cache read: {message.usage.cache_read_input_tokens}")
print(f"Cache creation: {message.usage.cache_creation_input_tokens}")
```

## Batches API
## 批处理 API

Process large volumes asynchronously at 50% cost reduction:
以50%成本降低异步处理大量数据：

```python
import time

batch = client.messages.batches.create(
    requests=[
        {
            "custom_id": f"request-{i}",
            "params": {
                "model": "claude-sonnet-4-0",
                "max_tokens": 1024,
                "messages": [{"role": "user", "content": prompt}]
            }
        }
        for i, prompt in enumerate(prompts)
    ]
)

# Poll for completion
# 轮询完成状态
while True:
    status = client.messages.batches.retrieve(batch.id)
    if status.processing_status == "ended":
        break
    time.sleep(30)

# Get results
# 获取结果
for result in client.messages.batches.results(batch.id):
    print(result.result.message.content[0].text)
```

## Claude Agent SDK
## Claude Agent SDK

Build multi-step agents:
构建多步骤智能体：

```python
# Note: Agent SDK API surface may change — check official docs
# 注意：Agent SDK API 可能会变更——请查看官方文档
import anthropic

# Define tools as functions
# 将工具定义为函数
tools = [{
    "name": "search_codebase",
    "description": "Search the codebase for relevant code",
    "input_schema": {
        "type": "object",
        "properties": {"query": {"type": "string"}},
        "required": ["query"]
    }
}]

# Run an agentic loop with tool use
# 使用工具调用运行智能体循环
client = anthropic.Anthropic()
messages = [{"role": "user", "content": "Review the auth module for security issues"}]

while True:
    response = client.messages.create(
        model="claude-sonnet-4-0",
        max_tokens=4096,
        tools=tools,
        messages=messages,
    )
    if response.stop_reason == "end_turn":
        break
    # Handle tool calls and continue the loop
    # 处理工具调用并继续循环
    messages.append({"role": "assistant", "content": response.content})
    # ... execute tools and append tool_result messages
    # ... 执行工具并附加 tool_result 消息
```

## Cost Optimization
## 成本优化

| Strategy | Savings | When to Use |
| 策略 | 节省 | 何时使用 |
|----------|---------|-------------|
| Prompt caching | Up to 90% on cached tokens | Repeated system prompts or context |
| 提示缓存 | 缓存令牌节省高达90% | 重复的系统提示或上下文 |
| Batches API | 50% | Non-time-sensitive bulk processing |
| 批处理 API | 50% | 非时间敏感的大量处理 |
| Haiku instead of Sonnet | ~75% | Simple tasks, classification, extraction |
| 使用 Haiku 而非 Sonnet | ~75% | 简单任务、分类、提取 |
| Shorter max_tokens | Variable | When you know output will be short |
| 更短的 max_tokens | 可变 | 当您知道输出会很短时 |
| Streaming | None (same cost) | Better UX, same price |
| 流式输出 | 无（相同成本） | 更好的用户体验，相同价格 |

## Error Handling
## 错误处理

```python
import time

from anthropic import APIError, RateLimitError, APIConnectionError

try:
    message = client.messages.create(...)
except RateLimitError:
    # Back off and retry
    # 退避并重试
    time.sleep(60)
except APIConnectionError:
    # Network issue, retry with backoff
    # 网络问题，退避重试
    pass
except APIError as e:
    print(f"API error {e.status_code}: {e.message}")
```

## Environment Setup
## 环境设置

```bash
# Required
# 必需
export ANTHROPIC_API_KEY="your-api-key-here"

# Optional: set default model
# 可选：设置默认模型
export ANTHROPIC_MODEL="claude-sonnet-4-0"
```

Never hardcode API keys. Always use environment variables.
切勿硬编码 API 密钥。始终使用环境变量。
