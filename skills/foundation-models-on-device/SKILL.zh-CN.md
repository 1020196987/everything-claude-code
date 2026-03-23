---
name: foundation-models-on-device
description: Apple FoundationModels framework for on-device LLM — text generation, guided generation with @Generable, tool calling, and snapshot streaming in iOS 26+.
description zh-CN: Apple FoundationModels框架用于设备端LLM——iOS 26+的文本生成、带@Generable的引导生成、工具调用和快照流式传输。
---

# FoundationModels: On-Device LLM (iOS 26)

## FoundationModels: On-Device LLM (iOS 26)
## FoundationModels: 设备端LLM（iOS 26）

Patterns for integrating Apple's on-device language model into apps using the FoundationModels framework. Covers text generation, structured output with `@Generable`, custom tool calling, and snapshot streaming — all running on-device for privacy and offline support.
使用FoundationModels框架将Apple设备端语言模型集成到应用中的模式。涵盖文本生成、带`@Generable`的结构化输出、自定义工具调用和快照流式传输——全部在设备上运行以保护隐私和支持离线。

## When to Activate

## When to Activate
## 何时激活

- Building AI-powered features using Apple Intelligence on-device
  使用Apple Intelligence设备端功能构建AI驱动特性
- Generating or summarizing text without cloud dependency
  在不依赖云的情况下生成或总结文本
- Extracting structured data from natural language input
  从自然语言输入中提取结构化数据
- Implementing custom tool calling for domain-specific AI actions
  为特定领域的AI操作实现自定义工具调用
- Streaming structured responses for real-time UI updates
  流式传输结构化响应以进行实时UI更新
- Need privacy-preserving AI (no data leaves the device)
  需要保护隐私的AI（数据不离开设备）

## Core Pattern — Availability Check

## Core Pattern — Availability Check
## 核心模式 — 可用性检查

Always check model availability before creating a session:
在创建会话前始终检查模型可用性：

```swift
struct GenerativeView: View {
    private var model = SystemLanguageModel.default

    var body: some View {
        switch model.availability {
        case .available:
            ContentView()
        case .unavailable(.deviceNotEligible):
            Text("Device not eligible for Apple Intelligence")
        case .unavailable(.appleIntelligenceNotEnabled):
            Text("Please enable Apple Intelligence in Settings")
        case .unavailable(.modelNotReady):
            Text("Model is downloading or not ready")
        case .unavailable(let other):
            Text("Model unavailable: \(other)")
        }
    }
}
```

## Core Pattern — Basic Session

## Core Pattern — Basic Session
## 核心模式 — 基本会话

```swift
// Single-turn: create a new session each time
let session = LanguageModelSession()
let response = try await session.respond(to: "What's a good month to visit Paris?")
print(response.content)

// Multi-turn: reuse session for conversation context
let session = LanguageModelSession(instructions: """
    You are a cooking assistant.
    Provide recipe suggestions based on ingredients.
    Keep suggestions brief and practical.
    """)

let first = try await session.respond(to: "I have chicken and rice")
let followUp = try await session.respond(to: "What about a vegetarian option?")
```

Key points for instructions:
指令的关键点：

- Define the model's role ("You are a mentor")
  定义模型的角色（"你是一位导师"）
- Specify what to do ("Help extract calendar events")
  指定要做什么（"帮助提取日历事件"）
- Set style preferences ("Respond as briefly as possible")
  设置风格偏好（"尽可能简洁回答"）
- Add safety measures ("Respond with 'I can't help with that' for dangerous requests")
  添加安全措施（"对危险请求回复'我无法帮助您'）

## Core Pattern — Guided Generation with @Generable

## Core Pattern — Guided Generation with @Generable
## 核心模式 — 带@Generable的引导生成

Generate structured Swift types instead of raw strings:
生成结构化的Swift类型而非原始字符串：

### 1. Define a Generable Type

### 1. Define a Generable Type
### 1. 定义Generable类型

```swift
@Generable(description: "Basic profile information about a cat")
struct CatProfile {
    var name: String

    @Guide(description: "The age of the cat", .range(0...20))
    var age: Int

    @Guide(description: "A one sentence profile about the cat's personality")
    var profile: String
}
```

### 2. Request Structured Output

### 2. Request Structured Output
### 2. 请求结构化输出

```swift
let response = try await session.respond(
    to: "Generate a cute rescue cat",
    generating: CatProfile.self
)

// Access structured fields directly
print("Name: \(response.content.name)")
print("Age: \(response.content.age)")
print("Profile: \(response.content.profile)")
```

### Supported @Guide Constraints

### Supported @Guide Constraints
### 支持的@Guide约束

- `.range(0...20)` — numeric range
  `.range(0...20)` — 数值范围
- `.count(3)` — array element count
  `.count(3)` — 数组元素数量
- `description:` — semantic guidance for generation
  `description:` — 生成的语义指导

## Core Pattern — Tool Calling

## Core Pattern — Tool Calling
## 核心模式 — 工具调用

Let the model invoke custom code for domain-specific tasks:
让模型调用自定义代码以执行特定领域的任务：

### 1. Define a Tool

### 1. Define a Tool
### 1. 定义工具

```swift
struct RecipeSearchTool: Tool {
    let name = "recipe_search"
    let description = "Search for recipes matching a given term and return a list of results."

    @Generable
    struct Arguments {
        var searchTerm: String
        var numberOfResults: Int
    }

    func call(arguments: Arguments) async throws -> ToolOutput {
        let recipes = await searchRecipes(
            term: arguments.searchTerm,
            limit: arguments.numberOfResults
        )
        return .string(recipes.map { "- \($0.name): \($0.description)" }.joined(separator: "\n"))
    }
}
```

### 2. Create Session with Tools

### 2. Create Session with Tools
### 2. 创建带工具的会话

```swift
let session = LanguageModelSession(tools: [RecipeSearchTool()])
let response = try await session.respond(to: "Find me some pasta recipes")
```

### 3. Handle Tool Errors

### 3. Handle Tool Errors
### 3. 处理工具错误

```swift
do {
    let answer = try await session.respond(to: "Find a recipe for tomato soup.")
} catch let error as LanguageModelSession.ToolCallError {
    print(error.tool.name)
    if case .databaseIsEmpty = error.underlyingError as? RecipeSearchToolError {
        // Handle specific tool error
    }
}
```

## Core Pattern — Snapshot Streaming

## Core Pattern — Snapshot Streaming
## 核心模式 — 快照流式传输

Stream structured responses for real-time UI with `PartiallyGenerated` types:
使用`PartiallyGenerated`类型流式传输结构化响应以实现实时UI：

```swift
@Generable
struct TripIdeas {
    @Guide(description: "Ideas for upcoming trips")
    var ideas: [String]
}

let stream = session.streamResponse(
    to: "What are some exciting trip ideas?",
    generating: TripIdeas.self
)

for try await partial in stream {
    // partial: TripIdeas.PartiallyGenerated (all properties Optional)
    print(partial)
}
```

### SwiftUI Integration

### SwiftUI Integration
### SwiftUI集成

```swift
@State private var partialResult: TripIdeas.PartiallyGenerated?
@State private var errorMessage: String?

var body: some View {
    List {
        ForEach(partialResult?.ideas ?? [], id: \.self) { idea in
            Text(idea)
        }
    }
    .overlay {
        if let errorMessage { Text(errorMessage).foregroundStyle(.red) }
    }
    .task {
        do {
            let stream = session.streamResponse(to: prompt, generating: TripIdeas.self)
            for try await partial in stream {
                partialResult = partial
            }
        } catch {
            errorMessage = error.localizedDescription
        }
    }
}
```

## Key Design Decisions

## Key Design Decisions
## 关键设计决策

| Decision | Rationale |
|----------|-----------|
| Decision | Rationale |
| 决策 | 理由 |
| On-device execution | Privacy — no data leaves the device; works offline |
| On-device execution | 隐私保护——数据不离开设备；支持离线 |
| 4,096 token limit | On-device model constraint; chunk large data across sessions |
| 4,096 token限制 | 设备端模型约束；将大数据分块到多个会话中 |
| Snapshot streaming (not deltas) | Structured output friendly; each snapshot is a complete partial state |
| 快照流式传输（非增量） | 结构化输出友好；每个快照都是完整的部分状态 |
| `@Generable` macro | Compile-time safety for structured generation; auto-generates `PartiallyGenerated` type |
| `@Generable`宏 | 结构化生成的编译时安全性；自动生成`PartiallyGenerated`类型 |
| Single request per session | `isResponding` prevents concurrent requests; create multiple sessions if needed |
| 每个会话单个请求 | `isResponding`防止并发请求；如有需要创建多个会话 |
| `response.content` (not `.output`) | Correct API — always access results via `.content` property |
| `response.content`（而非`.output`） | 正确的API——始终通过`.content`属性访问结果 |

## Best Practices

## Best Practices
## 最佳实践

- **Always check `model.availability`** before creating a session — handle all unavailability cases
  **始终检查`model.availability`** 在创建会话前——处理所有不可用情况
- **Use `instructions`** to guide model behavior — they take priority over prompts
  **使用`instructions`** 引导模型行为——它们优先于提示
- **Check `isResponding`** before sending a new request — sessions handle one request at a time
  **在发送新请求前检查`isResponding`** ——会话一次处理一个请求
- **Access `response.content`** for results — not `.output`
  **通过`response.content`** 访问结果——而非`.output`
- **Break large inputs into chunks** — 4,096 token limit applies to instructions + prompt + output combined
  **将大输入分块** ——4,096 token限制适用于指令 + 提示 + 输出的总和
- **Use `@Generable`** for structured output — stronger guarantees than parsing raw strings
  **使用`@Generable`** 进行结构化输出——比解析原始字符串更强的保证
- **Use `GenerationOptions(temperature:)`** to tune creativity (higher = more creative)
  **使用`GenerationOptions(temperature:)`** 调整创造力（越高=越有创意）
- **Monitor with Instruments** — use Xcode Instruments to profile request performance
  **使用Instruments监控** ——使用Xcode Instruments分析请求性能

## Anti-Patterns to Avoid

## Anti-Patterns to Avoid
## 应避免的反模式

- Creating sessions without checking `model.availability` first
  在首先不检查`model.availability`的情况下创建会话
- Sending inputs exceeding the 4,096 token context window
  发送超出4,096 token上下文窗口的输入
- Attempting concurrent requests on a single session
  在单个会话上尝试并发请求
- Using `.output` instead of `.content` to access response data
  使用`.output`而非`.content`访问响应数据
- Parsing raw string responses when `@Generable` structured output would work
  在`@Generable`结构化输出可用时解析原始字符串响应
- Building complex multi-step logic in a single prompt — break into multiple focused prompts
  在单个提示中构建复杂的多步逻辑——拆分为多个聚焦的提示
- Assuming the model is always available — device eligibility and settings vary
  假设模型始终可用——设备资格和设置各不相同

## When to Use

## When to Use
## 何时使用

- On-device text generation for privacy-sensitive apps
  隐私敏感应用的设备端文本生成
- Structured data extraction from user input (forms, natural language commands)
  从用户输入中提取结构化数据（表单、自然语言命令）
- AI-assisted features that must work offline
  必须支持离线的AI辅助功能
- Streaming UI that progressively shows generated content
  逐步显示生成内容的流式UI
- Domain-specific AI actions via tool calling (search, compute, lookup)
  通过工具调用实现特定领域的AI操作（搜索、计算、查询）
