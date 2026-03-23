---
name: x-api
description: X/Twitter API integration for posting tweets, threads, reading timelines, search, and analytics. Covers OAuth auth patterns, rate limits, and platform-native content posting. Use when the user wants to interact with X programmatically.
description zh-CN: X/Twitter API集成，用于发布推文、线程、读取时间线、搜索和分析。涵盖OAuth认证模式、速率限制和平台原生内容发布。当用户想要以编程方式与X交互时使用。
origin: ECC
---

# X API

## English

Programmatic interaction with X (Twitter) for posting, reading, searching, and analytics.

## 中文

以编程方式与X（Twitter）交互，用于发布、读取、搜索和分析。

## When to Activate / 何时激活

- User wants to post tweets or threads programmatically
- 用户想要以编程方式发布推文或线程
- Reading timeline, mentions, or user data from X
- 从X读取时间线、提及或用户数据
- Searching X for content, trends, or conversations
- 在X上搜索内容、趋势或对话
- Building X integrations or bots
- 构建X集成或机器人
- Analytics and engagement tracking
- 分析和参与度跟踪
- User says "post to X", "tweet", "X API", or "Twitter API"
- 用户说"post to X"、"tweet"、"X API"或"Twitter API"

## Authentication / 认证

### OAuth 2.0 Bearer Token (App-Only) / OAuth 2.0 Bearer Token（仅应用）

Best for: read-heavy operations, search, public data.
最适合：读取密集型操作、搜索、公开数据。

```bash
# Environment setup
# 环境设置
export X_BEARER_TOKEN="your-bearer-token"
```

```python
import os
import requests

bearer = os.environ["X_BEARER_TOKEN"]
headers = {"Authorization": f"Bearer {bearer}"}

# Search recent tweets
# 搜索最近推文
resp = requests.get(
    "https://api.x.com/2/tweets/search/recent",
    headers=headers,
    params={"query": "claude code", "max_results": 10}
)
tweets = resp.json()
```

### OAuth 1.0a (User Context) / OAuth 1.0a（用户上下文）

Required for: posting tweets, managing account, DMs.
需要用于：发布推文、管理账户、DM。

```bash
# Environment setup — source before use
# 环境设置 — 使用前source
export X_API_KEY="your-api-key"
export X_API_SECRET="your-api-secret"
export X_ACCESS_TOKEN="your-access-token"
export X_ACCESS_SECRET="your-access-secret"
```

```python
import os
from requests_oauthlib import OAuth1Session

oauth = OAuth1Session(
    os.environ["X_API_KEY"],
    client_secret=os.environ["X_API_SECRET"],
    resource_owner_key=os.environ["X_ACCESS_TOKEN"],
    resource_owner_secret=os.environ["X_ACCESS_SECRET"],
)
```

## Core Operations / 核心操作

### Post a Tweet / 发布推文

```python
resp = oauth.post(
    "https://api.x.com/2/tweets",
    json={"text": "Hello from Claude Code"}
)
resp.raise_for_status()
tweet_id = resp.json()["data"]["id"]
```

### Post a Thread / 发布线程

```python
def post_thread(oauth, tweets: list[str]) -> list[str]:
    ids = []
    reply_to = None
    for text in tweets:
        payload = {"text": text}
        if reply_to:
            payload["reply"] = {"in_reply_to_tweet_id": reply_to}
        resp = oauth.post("https://api.x.com/2/tweets", json=payload)
        tweet_id = resp.json()["data"]["id"]
        ids.append(tweet_id)
        reply_to = tweet_id
    return ids
```

### Read User Timeline / 读取用户时间线

```python
resp = requests.get(
    f"https://api.x.com/2/users/{user_id}/tweets",
    headers=headers,
    params={
        "max_results": 10,
        "tweet.fields": "created_at,public_metrics",
    }
)
```

### Search Tweets / 搜索推文

```python
resp = requests.get(
    "https://api.x.com/2/tweets/search/recent",
    headers=headers,
    params={
        "query": "from:affaanmustafa -is:retweet",
        "max_results": 10,
        "tweet.fields": "public_metrics,created_at",
    }
)
```

### Get User by Username / 通过用户名获取用户

```python
resp = requests.get(
    "https://api.x.com/2/users/by/username/affaanmustafa",
    headers=headers,
    params={"user.fields": "public_metrics,description,created_at"}
)
```

### Upload Media and Post / 上传媒体并发布

```python
# Media upload uses v1.1 endpoint
# 媒体上传使用v1.1端点

# Step 1: Upload media
# 步骤1：上传媒体
media_resp = oauth.post(
    "https://upload.twitter.com/1.1/media/upload.json",
    files={"media": open("image.png", "rb")}
)
media_id = media_resp.json()["media_id_string"]

# Step 2: Post with media
# 步骤2：带媒体发布
resp = oauth.post(
    "https://api.x.com/2/tweets",
    json={"text": "Check this out", "media": {"media_ids": [media_id]}}
)
```

## Rate Limits / 速率限制

X API rate limits vary by endpoint, auth method, and account tier, and they change over time. Always:
X API速率限制因端点、认证方法和账户级别而异，并且随时间变化。始终：
- Check the current X developer docs before hardcoding assumptions
- 在硬编码假设之前检查当前X开发者文档
- Read `x-rate-limit-remaining` and `x-rate-limit-reset` headers at runtime
- 在运行时读取`x-rate-limit-remaining`和`x-rate-limit-reset`头
- Back off automatically instead of relying on static tables in code
- 自动退避，而不是依赖代码中的静态表

```python
import time

remaining = int(resp.headers.get("x-rate-limit-remaining", 0))
if remaining < 5:
    reset = int(resp.headers.get("x-rate-limit-reset", 0))
    wait = max(0, reset - int(time.time()))
    print(f"Rate limit approaching. Resets in {wait}s")
```

## Error Handling / 错误处理

```python
resp = oauth.post("https://api.x.com/2/tweets", json={"text": content})
if resp.status_code == 201:
    return resp.json()["data"]["id"]
elif resp.status_code == 429:
    reset = int(resp.headers["x-rate-limit-reset"])
    raise Exception(f"Rate limited. Resets at {reset}")
elif resp.status_code == 403:
    raise Exception(f"Forbidden: {resp.json().get('detail', 'check permissions')}")
else:
    raise Exception(f"X API error {resp.status_code}: {resp.text}")
```

## Security / 安全

- **Never hardcode tokens.** Use environment variables or `.env` files.
  **永远不要硬编码令牌。**使用环境变量或`.env`文件。
- **Never commit `.env` files.** Add to `.gitignore`.
  **永远不要提交`.env`文件。**添加到`.gitignore`。
- **Rotate tokens** if exposed. Regenerate at developer.x.com.
  **如果暴露则轮换令牌。**在developer.x.com重新生成。
- **Use read-only tokens** when write access is not needed.
  **不需要写访问时使用只读令牌。**
- **Store OAuth secrets securely** — not in source code or logs.
  **安全存储OAuth密钥**——不在源代码或日志中。

## Integration with Content Engine / 与Content Engine集成

Use `content-engine` skill to generate platform-native content, then post via X API:
使用`content-engine` skill生成平台原生内容，然后通过X API发布：
1. Generate content with content-engine (X platform format)
1. 使用content-engine生成内容（X平台格式）
2. Validate length (280 chars for single tweet)
2. 验证长度（单推文280字符）
3. Post via X API using patterns above
3. 使用上述模式通过X API发布
4. Track engagement via public_metrics
4. 通过public_metrics跟踪参与度

## Related Skills / 相关Skills

- `content-engine` — Generate platform-native content for X
- `content-engine` — 为X生成平台原生内容
- `crosspost` — Distribute content across X, LinkedIn, and other platforms
- `crosspost` — 跨X、LinkedIn和其他平台分发内容
