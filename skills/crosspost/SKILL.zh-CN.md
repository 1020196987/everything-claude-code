---
name: crosspost
description: Multi-platform content distribution across X, LinkedIn, Threads, and Bluesky. Adapts content per platform using content-engine patterns. Never posts identical content cross-platform. Use when the user wants to distribute content across social platforms.
description zh-CN: 跨 X、LinkedIn、Threads 和 Bluesky 的多平台内容分发。使用 content-engine 模式按平台调整内容。绝不跨平台发布相同内容。在用户想要跨社交平台分发内容时使用。
origin: ECC
---

# Crosspost
# Crosspost
# 跨平台发布

Distribute content across multiple social platforms with platform-native adaptation.
## Distribute content across multiple social platforms with platform-native adaptation.
## 跨多个社交平台分发内容，进行平台原生适配。

## When to Activate
## When to Activate
## 何时激活

- User wants to post content to multiple platforms
  - 用户想要在多个平台发布内容
- Publishing announcements, launches, or updates across social media
  - 在社交媒体上发布公告、发布或更新
- Repurposing a post from one platform to others
  - 将一个平台的帖子改编到其他平台
- User says "crosspost", "post everywhere", "share on all platforms", or "distribute this"
  - 用户说"跨平台发布"、"到处发布"、"分享到所有平台"或"分发这个"

## Core Rules
## Core Rules
## 核心规则

1. **Never post identical content cross-platform.** Each platform gets a native adaptation.
   - **绝不跨平台发布相同内容。** 每个平台都进行原生适配。
2. **Primary platform first.** Post to the main platform, then adapt for others.
   - **先主平台。** 先发布到主平台，然后为其他平台改编。
3. **Respect platform conventions.** Length limits, formatting, link handling all differ.
   - **尊重平台惯例。** 长度限制、格式、链接处理各有不同。
4. **One idea per post.** If the source content has multiple ideas, split across posts.
   - **每个帖子一个想法。** 如果源内容有多个想法，请拆分到多个帖子。
5. **Attribution matters.** If crossposting someone else's content, credit the source.
   - **署名很重要。** 如果转发他人内容，请注明来源。

## Platform Specifications
## Platform Specifications
## 平台规格

| Platform | Max Length | Link Handling | Hashtags | Media |
|----------|-----------|---------------|----------|-------|
| X | 280 chars (4000 for Premium) | Counted in length | Minimal (1-2 max) | Images, video, GIFs |
| X | 280 字符（Premium 为 4000） | 计入长度 | 最小化（最多 1-2 个） | 图片、视频、GIF |
| LinkedIn | 3000 chars | Not counted in length | 3-5 relevant | Images, video, docs, carousels |
| LinkedIn | 3000 字符 | 不计入长度 | 3-5 个相关 | 图片、视频、文档、轮播 |
| Threads | 500 chars | Separate link attachment | None typical | Images, video |
| Threads | 500 字符 | 单独链接附件 | 通常无 | 图片、视频 |
| Bluesky | 300 chars | Via facets (rich text) | None (use feeds) | Images |
| Bluesky | 300 字符 | 通过 facets（富文本） | 无（使用 feeds） | 图片 |

## Workflow
## Workflow
## 工作流

### Step 1: Create Source Content
### Step 1: Create Source Content
### 步骤 1：创建源内容

Start with the core idea. Use `content-engine` skill for high-quality drafts:
## Start with the core idea. Use `content-engine` skill for high-quality drafts.
## 从核心想法开始。使用 `content-engine` 技能生成高质量草稿：

- Identify the single core message
  - 识别单一核心信息
- Determine the primary platform (where the audience is biggest)
  - 确定主平台（受众最大的平台）
- Draft the primary platform version first
  - 先起草主平台版本

### Step 2: Identify Target Platforms
### Step 2: Identify Target Platforms
### 步骤 2：确定目标平台

Ask the user or determine from context:
## Ask the user or determine from context.
## 询问用户或从上下文确定：

- Which platforms to target
  - 目标哪些平台
- Priority order (primary gets the best version)
  - 优先级顺序（主平台获得最佳版本）
- Any platform-specific requirements (e.g., LinkedIn needs professional tone)
  - 任何特定平台的要求（例如 LinkedIn 需要专业语气）

### Step 3: Adapt Per Platform
### Step 3: Adapt Per Platform
### 步骤 3：按平台改编

For each target platform, transform the content:
## For each target platform, transform the content.
## 对每个目标平台，转换内容：

**X adaptation:**
## **X adaptation:**
## **X 适配：**

- Open with a hook, not a summary
  - 以 hook 开头，而非总结
- Cut to the core insight fast
  - 快速切入核心洞察
- Keep links out of main body when possible
  - 尽可能将链接放在正文之外
- Use thread format for longer content
  - 对较长内容使用推文串格式

**LinkedIn adaptation:**
## **LinkedIn adaptation:**
## **LinkedIn 适配：**

- Strong first line (visible before "see more")
  - 强有力的第一行（"查看更多"前可见）
- Short paragraphs with line breaks
  - 带换行的短段落
- Frame around lessons, results, or professional takeaways
  - 以经验、成果或专业收获为框架
- More explicit context than X (LinkedIn audience needs framing)
  - 比 X 更明确的上下文（LinkedIn 受众需要框架）

**Threads adaptation:**
## **Threads adaptation:**
## **Threads 适配：**

- Conversational, casual tone
  - 对话式、随意语气
- Shorter than LinkedIn, less compressed than X
  - 比 LinkedIn 短，比 X 压缩少
- Visual-first if possible
  - 尽可能以视觉优先

**Bluesky adaptation:**
## **Bluesky adaptation:**
## **Bluesky 适配：**

- Direct and concise (300 char limit)
  - 直接且简洁（300 字符限制）
- Community-oriented tone
  - 社区导向的语气
- Use feeds/lists for topic targeting instead of hashtags
  - 使用 feeds/lists 进行主题定位而非 hashtag

### Step 4: Post Primary Platform
### Step 4: Post Primary Platform
### 步骤 4：发布主平台

Post to the primary platform first:
## Post to the primary platform first.
## 先发布到主平台：

- Use `x-api` skill for X
  - 对 X 使用 `x-api` 技能
- Use platform-specific APIs or tools for others
  - 对其他使用平台特定 API 或工具
- Capture the post URL for cross-referencing
  - 捕获帖子 URL 以便交叉引用

### Step 5: Post to Secondary Platforms
### Step 5: Post to Secondary Platforms
### 步骤 5：发布到次要平台

Post adapted versions to remaining platforms:
## Post adapted versions to remaining platforms.
## 向剩余平台发布改编版本：

- Stagger timing (not all at once — 30-60 min gaps)
  - 错开发布时间（不要一次性全部发布——间隔 30-60 分钟）
- Include cross-platform references where appropriate ("longer thread on X" etc.)
  - 在适当的地方包含跨平台引用（"在 X 上查看更长的推文串"等）

## Content Adaptation Examples
## Content Adaptation Examples
## 内容改编示例

### Source: Product Launch
### Source: Product Launch
### 源：产品发布

**X version:**
## **X version:**
## **X 版本：**

```
We just shipped [feature].

[One specific thing it does that's impressive]

[Link]
```

**LinkedIn version:**
## **LinkedIn version:**
## **LinkedIn 版本：**

```
Excited to share: we just launched [feature] at [Company].

Here's why it matters:

[2-3 short paragraphs with context]

[Takeaway for the audience]

[Link]
```

**Threads version:**
## **Threads version:**
## **Threads 版本：**

```
just shipped something cool — [feature]

[casual explanation of what it does]

link in bio
```

### Source: Technical Insight
### Source: Technical Insight
### 源：技术洞察

**X version:**
## **X version:**
## **X 版本：**

```
TIL: [specific technical insight]

[Why it matters in one sentence]
```

**LinkedIn version:**
## **LinkedIn version:**
## **LinkedIn 版本：**

```
A pattern I've been using that's made a real difference:

[Technical insight with professional framing]

[How it applies to teams/orgs]

#relevantHashtag
```

## API Integration
## API Integration
## API 集成

### Batch Crossposting Service (Example Pattern)
### Batch Crossposting Service (Example Pattern)
### 批量跨平台发布服务（示例模式）

If using a crossposting service (e.g., Postbridge, Buffer, or a custom API), the pattern looks like:
## If using a crossposting service (e.g., Postbridge, Buffer, or a custom API), the pattern looks like:
## 如果使用跨平台发布服务（例如 Postbridge、Buffer 或自定义 API），模式如下：

```python
import os
import requests

resp = requests.post(
    "https://your-crosspost-service.example/api/posts",
    headers={"Authorization": f"Bearer {os.environ['POSTBRIDGE_API_KEY']}"},
    json={
        "platforms": ["twitter", "linkedin", "threads"],
        "content": {
            "twitter": {"text": x_version},
            "linkedin": {"text": linkedin_version},
            "threads": {"text": threads_version}
        }
    },
    timeout=30,
)
resp.raise_for_status()
```

### Manual Posting
### Manual Posting
### 手动发布

Without Postbridge, post to each platform using its native API:
## Without Postbridge, post to each platform using its native API.
## 没有 Postbridge 时，使用每个平台的原生 API 发布：

- X: Use `x-api` skill patterns
  - X：使用 `x-api` 技能模式
- LinkedIn: LinkedIn API v2 with OAuth 2.0
  - LinkedIn：LinkedIn API v2 配合 OAuth 2.0
- Threads: Threads API (Meta)
  - Threads：Threads API（Meta）
- Bluesky: AT Protocol API
  - Bluesky：AT Protocol API

## Quality Gate
## Quality Gate
## 质量门

Before posting:
## Before posting:
## 发布前：

- [ ] Each platform version reads naturally for that platform
  - [ ] 每个平台版本在该平台上阅读自然
- [ ] No identical content across platforms
  - [ ] 各平台没有相同内容
- [ ] Length limits respected
  - [ ] 遵守长度限制
- [ ] Links work and are placed appropriately
  - [ ] 链接有效且放置适当
- [ ] Tone matches platform conventions
  - [ ] 语气符合平台惯例
- [ ] Media is sized correctly for each platform
  - [ ] 媒体尺寸适合每个平台

## Related Skills
## Related Skills
## 相关技能

- `content-engine` — Generate platform-native content
  - `content-engine` — 生成平台原生内容
- `x-api` — X/Twitter API integration
  - `x-api` — X/Twitter API 集成
