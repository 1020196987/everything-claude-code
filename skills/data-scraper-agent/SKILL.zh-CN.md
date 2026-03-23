---
name: data-scraper-agent
description: Build a fully automated AI-powered data collection agent for any public source — job boards, prices, news, GitHub, sports, anything. Scrapes on a schedule, enriches data with a free LLM (Gemini Flash), stores results in Notion/Sheets/Supabase, and learns from user feedback. Runs 100% free on GitHub Actions. Use when the user wants to monitor, collect, or track any public data automatically.
description zh-CN: 构建一个完全自动化的 AI 驱动的数据采集 agent，用于任何公开来源——求职板、价格、新闻、GitHub、体育，任何内容。按计划抓取，使用免费 LLM（Gemini Flash）丰富数据，存储结果到 Notion/Sheets/Supabase，并根据用户反馈学习。100% 免费运行在 GitHub Actions 上。当用户想要自动监控、收集或跟踪任何公开数据时使用。
origin: community
---

# Data Scraper Agent
# Data Scraper Agent
# 数据采集 Agent

Build a production-ready, AI-powered data collection agent for any public data source.
## Build a production-ready, AI-powered data collection agent for any public data source.
## 为任何公开数据源构建一个生产就绪的 AI 驱动的数据采集 agent。

Runs on a schedule, enriches results with a free LLM, stores to a database, and improves over time.
## Runs on a schedule, enriches results with a free LLM, stores to a database, and improves over time.
## 按计划运行，使用免费 LLM 丰富结果，存储到数据库，并随时间改进。

**Stack: Python · Gemini Flash (free) · GitHub Actions (free) · Notion / Sheets / Supabase**
**Stack: Python · Gemini Flash (free) · GitHub Actions (free) · Notion / Sheets / Supabase**
**技术栈：Python · Gemini Flash（免费）· GitHub Actions（免费）· Notion / Sheets / Supabase**

## When to Activate
## When to Activate
## 何时激活

- User wants to scrape or monitor any public website or API
  - 用户想要抓取或监控任何公开网站或 API
- User says "build a bot that checks...", "monitor X for me", "collect data from..."
  - 用户说"构建一个检查...的机器人"、"帮我监控 X"、"从...收集数据"
- User wants to track jobs, prices, news, repos, sports scores, events, listings
  - 用户想要跟踪工作、价格、新闻、仓库、体育比分、活动、列表
- User asks how to automate data collection without paying for hosting
  - 用户询问如何在不支付托管费用的情况下自动化数据收集
- User wants an agent that gets smarter over time based on their decisions
  - 用户想要一个根据他们决策随时间变得更智能的 agent

## Core Concepts
## Core Concepts
## 核心概念

### The Three Layers
### The Three Layers
### 三层架构

Every data scraper agent has three layers:
## Every data scraper agent has three layers.
## 每个数据采集 agent 都有三层：

```
COLLECT → ENRICH → STORE
  │           │        │
Scraper    AI (LLM)  Database
runs on    scores/   Notion /
schedule   summarises Sheets /
           & classifies Supabase
```

### Free Stack
### Free Stack
### 免费技术栈

| Layer | Tool | Why |
|---|---|---|
| **Scraping** | `requests` + `BeautifulSoup` | No cost, covers 80% of public sites |
| **Scraping** | `requests` + `BeautifulSoup` | 无成本，覆盖 80% 的公开网站 |
| **JS-rendered sites** | `playwright` (free) | When HTML scraping fails |
| **JS-rendered sites** | `playwright`（免费） | 当 HTML 抓取失败时 |
| **AI enrichment** | Gemini Flash via REST API | 500 req/day, 1M tokens/day — free |
| **AI enrichment** | 通过 REST API 的 Gemini Flash | 500 请求/天，100 万 token/天——免费 |
| **Storage** | Notion API | Free tier, great UI for review |
| **Storage** | Notion API | 免费层，审查的出色 UI |
| **Schedule** | GitHub Actions cron | Free for public repos |
| **Schedule** | GitHub Actions cron | 公共仓库免费 |
| **Learning** | JSON feedback file in repo | Zero infra, persists in git |
| **Learning** | 仓库中的 JSON 反馈文件 | 零基础设施，持久化在 git 中 |

### AI Model Fallback Chain
### AI Model Fallback Chain
### AI 模型回退链

Build agents to auto-fallback across Gemini models on quota exhaustion:
## Build agents to auto-fallback across Gemini models on quota exhaustion.
## 构建 agent 以在配额耗尽时自动跨 Gemini 模型回退：

```
gemini-2.0-flash-lite (30 RPM) →
gemini-2.0-flash (15 RPM) →
gemini-2.5-flash (10 RPM) →
gemini-flash-lite-latest (fallback)
```

### Batch API Calls for Efficiency
### Batch API Calls for Efficiency
### 批量 API 调用以提高效率

Never call the LLM once per item. Always batch:
## Never call the LLM once per item. Always batch.
## 永远不要每个项目调用一次 LLM。始终批量处理：

```python
# BAD: 33 API calls for 33 items
# 错误：33 个项目 33 次 API 调用
for item in items:
    result = call_ai(item)  # 33 calls → hits rate limit

# GOOD: 7 API calls for 33 items (batch size 5)
# 正确：33 个项目 7 次 API 调用（批量大小 5）
for batch in chunks(items, size=5):
    results = call_ai(batch)  # 7 calls → stays within free tier
```

---

## Workflow
## Workflow
## 工作流

### Step 1: Understand the Goal
### Step 1: Understand the Goal
### 步骤 1：了解目标

Ask the user:
## Ask the user.
## 询问用户：

1. **What to collect:** "What data source? URL / API / RSS / public endpoint?"
   - **收集什么：** "什么数据源？URL / API / RSS / 公开端点？"
2. **What to extract:** "What fields matter? Title, price, URL, date, score?"
   - **提取什么：** "哪些字段重要？标题、价格、URL、日期、分数？"
3. **How to store:** "Where should results go? Notion, Google Sheets, Supabase, or local file?"
   - **如何存储：** "结果应该存到哪里？Notion、Google Sheets、Supabase 还是本地文件？"
4. **How to enrich:** "Do you want AI to score, summarise, classify, or match each item?"
   - **如何丰富：** "你想要 AI 对每个项目评分、总结、分类还是匹配？"
5. **Frequency:** "How often should it run? Every hour, daily, weekly?"
   - **频率：** "应该多久运行一次？每小时、每天还是每周？"

Common examples to prompt:
## Common examples to prompt.
## 常见示例提示：

- Job boards → score relevance to resume
  - 求职板 → 简历相关性评分
- Product prices → alert on drops
  - 产品价格 → 价格下降警报
- GitHub repos → summarise new releases
  - GitHub 仓库 → 总结新发布
- News feeds → classify by topic + sentiment
  - 新闻源 → 按主题 + 情感分类
- Sports results → extract stats to tracker
  - 体育结果 → 提取统计数据到跟踪器
- Events calendar → filter by interest
  - 活动日历 → 按兴趣筛选

---

### Step 2: Design the Agent Architecture
### Step 2: Design the Agent Architecture
### 步骤 2：设计 Agent 架构

Generate this directory structure for the user:
## Generate this directory structure for the user.
## 为用户生成此目录结构：

```
my-agent/
├── config.yaml              # User customises this (keywords, filters, preferences)
  # 用户自定义（关键词、过滤器、偏好）
├── profile/
│   └── context.md           # User context the AI uses (resume, interests, criteria)
  # AI 使用的用户上下文（简历、兴趣、标准）
├── scraper/
│   ├── __init__.py
│   ├── main.py              # Orchestrator: scrape → enrich → store
  # 编排器：抓取 → 丰富 → 存储
│   ├── filters.py           # Rule-based pre-filter (fast, before AI)
  # 基于规则的预过滤器（快速，在 AI 之前）
│   └── sources/
│       ├── __init__.py
│       └── source_name.py   # One file per data source
  # 每个数据源一个文件
├── ai/
│   ├── __init__.py
│   ├── client.py            # Gemini REST client with model fallback
  # 带模型回退的 Gemini REST 客户端
│   ├── pipeline.py          # Batch AI analysis
  # 批量 AI 分析
│   ├── jd_fetcher.py        # Fetch full content from URLs (optional)
  # 从 URL 获取完整内容（可选）
│   └── memory.py            # Learn from user feedback
  # 从用户反馈中学习
├── storage/
│   ├── __init__.py
│   └── notion_sync.py       # Or sheets_sync.py / supabase_sync.py
├── data/
│   └── feedback.json        # User decision history (auto-updated)
  # 用户决策历史（自动更新）
├── .env.example
├── setup.py                 # One-time DB/schema creation
  # 一次性 DB/模式创建
├── enrich_existing.py       # Backfill AI scores on old rows
  # 在旧行上回填 AI 分数
├── requirements.txt
└── .github/
    └── workflows/
        └── scraper.yml      # GitHub Actions schedule
```

---

### Step 3: Build the Scraper Source
### Step 3: Build the Scraper Source
### 步骤 3：构建抓取源

Template for any data source:
## Template for any data source.
## 任何数据源的模板：

```python
# scraper/sources/my_source.py
"""
[Source Name] — scrapes [what] from [where].
Method: [REST API / HTML scraping / RSS feed]
"""
import requests
from bs4 import BeautifulSoup
from datetime import datetime, timezone
from scraper.filters import is_relevant

HEADERS = {
    "User-Agent": "Mozilla/5.0 (compatible; research-bot/1.0)",
}


def fetch() -> list[dict]:
    """
    Returns a list of items with consistent schema.
    Each item must have at minimum: name, url, date_found.
    """
    results = []

    # ---- REST API source ----
    resp = requests.get("https://api.example.com/items", headers=HEADERS, timeout=15)
    if resp.status_code == 200:
        for item in resp.json().get("results", []):
            if not is_relevant(item.get("title", "")):
                continue
            results.append(_normalise(item))

    return results


def _normalise(raw: dict) -> dict:
    """Convert raw API/HTML data to the standard schema."""
    return {
        "name": raw.get("title", ""),
        "url": raw.get("link", ""),
        "source": "MySource",
        "date_found": datetime.now(timezone.utc).date().isoformat(),
        # add domain-specific fields here
    }
```

**HTML scraping pattern:**
## **HTML scraping pattern:**
## **HTML 抓取模式：**

```python
soup = BeautifulSoup(resp.text, "lxml")
for card in soup.select("[class*='listing']"):
    title = card.select_one("h2, h3").get_text(strip=True)
    link = card.select_one("a")["href"]
    if not link.startswith("http"):
        link = f"https://example.com{link}"
```

**RSS feed pattern:**
## **RSS feed pattern:**
## **RSS 订阅源模式：**

```python
import xml.etree.ElementTree as ET
root = ET.fromstring(resp.text)
for item in root.findall(".//item"):
    title = item.findtext("title", "")
    link = item.findtext("link", "")
```

---

### Step 4: Build the Gemini AI Client
### Step 4: Build the Gemini AI Client
### 步骤 4：构建 Gemini AI 客户端

```python
# ai/client.py
import os, json, time, requests

_last_call = 0.0

MODEL_FALLBACK = [
    "gemini-2.0-flash-lite",
    "gemini-2.0-flash",
    "gemini-2.5-flash",
    "gemini-flash-lite-latest",
]


def generate(prompt: str, model: str = "", rate_limit: float = 7.0) -> dict:
    """Call Gemini with auto-fallback on 429. Returns parsed JSON or {}."""
    global _last_call

    api_key = os.environ.get("GEMINI_API_KEY", "")
    if not api_key:
        return {}

    elapsed = time.time() - _last_call
    if elapsed < rate_limit:
        time.sleep(rate_limit - elapsed)

    models = [model] + [m for m in MODEL_FALLBACK if m != model] if model else MODEL_FALLBACK
    _last_call = time.time()

    for m in models:
        url = f"https://generativelanguage.googleapis.com/v1beta/models/{m}:generateContent?key={api_key}"
        payload = {
            "contents": [{"parts": [{"text": prompt}]}],
            "generationConfig": {
                "responseMimeType": "application/json",
                "temperature": 0.3,
                "maxOutputTokens": 2048,
            },
        }
        try:
            resp = requests.post(url, json=payload, timeout=30)
            if resp.status_code == 200:
                return _parse(resp)
            if resp.status_code in (429, 404):
                time.sleep(1)
                continue
            return {}
        except requests.RequestException:
            return {}

    return {}


def _parse(resp) -> dict:
    try:
        text = (
            resp.json()
            .get("candidates", [{}])[0]
            .get("content", {})
            .get("parts", [{}])[0]
            .get("text", "")
            .strip()
        )
        if text.startswith("```"):
            text = text.split("\n", 1)[-1].rsplit("```", 1)[0]
        return json.loads(text)
    except (json.JSONDecodeError, KeyError):
        return {}
```

---

### Step 5: Build the AI Pipeline (Batch)
### Step 5: Build the AI Pipeline (Batch)
### 步骤 5：构建 AI 管道（批量）

```python
# ai/pipeline.py
import json
import yaml
from pathlib import Path
from ai.client import generate

def analyse_batch(items: list[dict], context: str = "", preference_prompt: str = "") -> list[dict]:
    """Analyse items in batches. Returns items enriched with AI fields."""
    config = yaml.safe_load((Path(__file__).parent.parent / "config.yaml").read_text())
    model = config.get("ai", {}).get("model", "gemini-2.5-flash")
    rate_limit = config.get("ai", {}).get("rate_limit_seconds", 7.0)
    min_score = config.get("ai", {}).get("min_score", 0)
    batch_size = config.get("ai", {}).get("batch_size", 5)

    batches = [items[i:i + batch_size] for i in range(0, len(items), batch_size)]
    print(f"  [AI] {len(items)} items → {len(batches)} API calls")

    enriched = []
    for i, batch in enumerate(batches):
        print(f"  [AI] Batch {i + 1}/{len(batches)}...")
        prompt = _build_prompt(batch, context, preference_prompt, config)
        result = generate(prompt, model=model, rate_limit=rate_limit)

        analyses = result.get("analyses", [])
        for j, item in enumerate(batch):
            ai = analyses[j] if j < len(analyses) else {}
            if ai:
                score = max(0, min(100, int(ai.get("score", 0))))
                if min_score and score < min_score:
                    continue
                enriched.append({**item, "ai_score": score, "ai_summary": ai.get("summary", ""), "ai_notes": ai.get("notes", "")})
            else:
                enriched.append(item)

    return enriched


def _build_prompt(batch, context, preference_prompt, config):
    priorities = config.get("priorities", [])
    items_text = "\n\n".join(
        f"Item {i+1}: {json.dumps({k: v for k, v in item.items() if not k.startswith('_')})}"
        for i, item in enumerate(batch)
    )

    return f"""Analyse these {len(batch)} items and return a JSON object.

# Items
{items_text}

# User Context
{context[:800] if context else "Not provided"}

# User Priorities
{chr(10).join(f"- {p}" for p in priorities)}

{preference_prompt}

# Instructions
Return: {{"analyses": [{{"score": <0-100>, "summary": "<2 sentences>", "notes": "<why this matches or doesn't>"}} for each item in order]}}
Be concise. Score 90+=excellent match, 70-89=good, 50-69=ok, <50=weak."""
```

---

### Step 6: Build the Feedback Learning System
### Step 6: Build the Feedback Learning System
### 步骤 6：构建反馈学习系统

```python
# ai/memory.py
"""Learn from user decisions to improve future scoring."""
import json
from pathlib import Path

FEEDBACK_PATH = Path(__file__).parent.parent / "data" / "feedback.json"


def load_feedback() -> dict:
    if FEEDBACK_PATH.exists():
        try:
            return json.loads(FEEDBACK_PATH.read_text())
        except (json.JSONDecodeError, OSError):
            pass
    return {"positive": [], "negative": []}


def save_feedback(fb: dict):
    FEEDBACK_PATH.parent.mkdir(parents=True, exist_ok=True)
    FEEDBACK_PATH.write_text(json.dumps(fb, indent=2))


def build_preference_prompt(feedback: dict, max_examples: int = 15) -> str:
    """Convert feedback history into a prompt bias section."""
    lines = []
    if feedback.get("positive"):
        lines.append("# Items the user LIKED (positive signal):")
        for e in feedback["positive"][-max_examples:]:
            lines.append(f"- {e}")
    if feedback.get("negative"):
        lines.append("\n# Items the user SKIPPED/REJECTED (negative signal):")
        for e in feedback["negative"][-max_examples:]:
            lines.append(f"- {e}")
    if lines:
        lines.append("\nUse these patterns to bias scoring on new items.")
    return "\n".join(lines)
```

**Integration with your storage layer:** after each run, query your DB for items with positive/negative status and call `save_feedback()` with the extracted patterns.
## **Integration with your storage layer:** after each run, query your DB for items with positive/negative status and call `save_feedback()` with the extracted patterns.
## **与存储层集成：** 每次运行后，查询 DB 中具有 positive/negative 状态的项目，并用提取的模式调用 `save_feedback()`。

---

### Step 7: Build Storage (Notion example)
### Step 7: Build Storage (Notion example)
### 步骤 7：构建存储（Notion 示例）

```python
# storage/notion_sync.py
import os
from notion_client import Client
from notion_client.errors import APIResponseError

_client = None

def get_client():
    global _client
    if _client is None:
        _client = Client(auth=os.environ["NOTION_TOKEN"])
    return _client

def get_existing_urls(db_id: str) -> set[str]:
    """Fetch all URLs already stored — used for deduplication."""
    client, seen, cursor = get_client(), set(), None
    while True:
        resp = client.databases.query(database_id=db_id, page_size=100, **{"start_cursor": cursor} if cursor else {})
        for page in resp["results"]:
            url = page["properties"].get("URL", {}).get("url", "")
            if url: seen.add(url)
        if not resp["has_more"]: break
        cursor = resp["next_cursor"]
    return seen

def push_item(db_id: str, item: dict) -> bool:
    """Push one item to Notion. Returns True on success."""
    props = {
        "Name": {"title": [{"text": {"content": item.get("name", "")[:100]}}]},
        "URL": {"url": item.get("url")},
        "Source": {"select": {"name": item.get("source", "Unknown")}},
        "Date Found": {"date": {"start": item.get("date_found")}},
        "Status": {"select": {"name": "New"}},
    }
    # AI fields
    if item.get("ai_score") is not None:
        props["AI Score"] = {"number": item["ai_score"]}
    if item.get("ai_summary"):
        props["Summary"] = {"rich_text": [{"text": {"content": item["ai_summary"][:2000]}}]}
    if item.get("ai_notes"):
        props["Notes"] = {"rich_text": [{"text": {"content": item["ai_notes"][:2000]}}]}

    try:
        get_client().pages.create(parent={"database_id": db_id}, properties=props)
        return True
    except APIResponseError as e:
        print(f"[notion] Push failed: {e}")
        return False

def sync(db_id: str, items: list[dict]) -> tuple[int, int]:
    existing = get_existing_urls(db_id)
    added = skipped = 0
    for item in items:
        if item.get("url") in existing:
            skipped += 1; continue
        if push_item(db_id, item):
            added += 1; existing.add(item["url"])
        else:
            skipped += 1
    return added, skipped
```

---

### Step 8: Orchestrate in main.py
### Step 8: Orchestrate in main.py
### 步骤 8：在 main.py 中编排

```python
# scraper/main.py
import os, sys, yaml
from pathlib import Path
from dotenv import load_dotenv

load_dotenv()

from scraper.sources import my_source          # add your sources

# NOTE: This example uses Notion. If storage.provider is "sheets" or "supabase",
# replace this import with storage.sheets_sync or storage.supabase_sync and update
# the env var and sync() call accordingly.
from storage.notion_sync import sync

SOURCES = [
    ("My Source", my_source.fetch),
]

def ai_enabled():
    return bool(os.environ.get("GEMINI_API_KEY"))

def main():
    config = yaml.safe_load((Path(__file__).parent.parent / "config.yaml").read_text())
    provider = config.get("storage", {}).get("provider", "notion")

    # Resolve the storage target identifier from env based on provider
    if provider == "notion":
        db_id = os.environ.get("NOTION_DATABASE_ID")
        if not db_id:
            print("ERROR: NOTION_DATABASE_ID not set"); sys.exit(1)
    else:
        # Extend here for sheets (SHEET_ID) or supabase (SUPABASE_TABLE) etc.
        print(f"ERROR: provider '{provider}' not yet wired in main.py"); sys.exit(1)

    config = yaml.safe_load((Path(__file__).parent.parent / "config.yaml").read_text())
    all_items = []

    for name, fetch_fn in SOURCES:
        try:
            items = fetch_fn()
            print(f"[{name}] {len(items)} items")
            all_items.extend(items)
        except Exception as e:
            print(f"[{name}] FAILED: {e}")

    # Deduplicate by URL
    seen, deduped = set(), []
    for item in all_items:
        if (url := item.get("url", "")) and url not in seen:
            seen.add(url); deduped.append(item)

    print(f"Unique items: {len(deduped)}")

    if ai_enabled() and deduped:
        from ai.memory import load_feedback, build_preference_prompt
        from ai.pipeline import analyse_batch

        # load_feedback() reads data/feedback.json written by your feedback sync script.
        # To keep it current, implement a separate feedback_sync.py that queries your
        # storage provider for items with positive/negative statuses and calls save_feedback().
        feedback = load_feedback()
        preference = build_preference_prompt(feedback)
        context_path = Path(__file__).parent.parent / "profile" / "context.md"
        context = context_path.read_text() if context_path.exists() else ""
        deduped = analyse_batch(deduped, context=context, preference_prompt=preference)
    else:
        print("[AI] Skipped — GEMINI_API_KEY not set")

    added, skipped = sync(db_id, deduped)
    print(f"Done — {added} new, {skipped} existing")

if __name__ == "__main__":
    main()
```

---

### Step 9: GitHub Actions Workflow
### Step 9: GitHub Actions Workflow
### 步骤 9：GitHub Actions 工作流

```yaml
# .github/workflows/scraper.yml
name: Data Scraper Agent

on:
  schedule:
    - cron: "0 */3 * * *"  # every 3 hours — adjust to your needs
  workflow_dispatch:        # allow manual trigger

permissions:
  contents: write   # required for the feedback-history commit step

jobs:
  scrape:
    runs-on: ubuntu-latest
    timeout-minutes: 20

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-python@v5
        with:
          python-version: "3.11"
          cache: "pip"

      - run: pip install -r requirements.txt

      # Uncomment if Playwright is enabled in requirements.txt
      # - name: Install Playwright browsers
      #   run: python -m playwright install chromium --with-deps

      - name: Run agent
        env:
          NOTION_TOKEN: ${{ secrets.NOTION_TOKEN }}
          NOTION_DATABASE_ID: ${{ secrets.NOTION_DATABASE_ID }}
          GEMINI_API_KEY: ${{ secrets.GEMINI_API_KEY }}
        run: python -m scraper.main

      - name: Commit feedback history
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git add data/feedback.json || true
          git diff --cached --quiet || git commit -m "chore: update feedback history"
          git push
```

---

### Step 10: config.yaml Template
### Step 10: config.yaml Template
### 步骤 10：config.yaml 模板

```yaml
# Customise this file — no code changes needed
# 自定义此文件——无需更改代码

# What to collect (pre-filter before AI)
# 要收集的内容（AI 前的预过滤）
filters:
  required_keywords: []      # item must contain at least one
  blocked_keywords: []       # item must not contain any

# Your priorities — AI uses these for scoring
# 你的优先级——AI 用这些来评分
priorities:
  - "example priority 1"
  - "example priority 2"

# Storage
# 存储
storage:
  provider: "notion"         # notion | sheets | supabase | sqlite

# Feedback learning
# 反馈学习
feedback:
  positive_statuses: ["Saved", "Applied", "Interested"]
  negative_statuses: ["Skip", "Rejected", "Not relevant"]

# AI settings
# AI 设置
ai:
  enabled: true
  model: "gemini-2.5-flash"
  min_score: 0               # filter out items below this score
  rate_limit_seconds: 7      # seconds between API calls
  batch_size: 5              # items per API call
```

---

## Common Scraping Patterns
## Common Scraping Patterns
## 常见抓取模式

### Pattern 1: REST API (easiest)
### Pattern 1: REST API (easiest)
### 模式 1：REST API（最简单）

```python
resp = requests.get(url, params={"q": query}, headers=HEADERS, timeout=15)
items = resp.json().get("results", [])
```

### Pattern 2: HTML Scraping
### Pattern 2: HTML Scraping
### 模式 2：HTML 抓取

```python
soup = BeautifulSoup(resp.text, "lxml")
for card in soup.select(".listing-card"):
    title = card.select_one("h2").get_text(strip=True)
    href = card.select_one("a")["href"]
```

### Pattern 3: RSS Feed
### Pattern 3: RSS Feed
### 模式 3：RSS 订阅源

```python
import xml.etree.ElementTree as ET
root = ET.fromstring(resp.text)
for item in root.findall(".//item"):
    title = item.findtext("title", "")
    link = item.findtext("link", "")
    pub_date = item.findtext("pubDate", "")
```

### Pattern 4: Paginated API
### Pattern 4: Paginated API
### 模式 4：分页 API

```python
page = 1
while True:
    resp = requests.get(url, params={"page": page, "limit": 50}, timeout=15)
    data = resp.json()
    items = data.get("results", [])
    if not items:
        break
    for item in items:
        results.append(_normalise(item))
    if not data.get("has_more"):
        break
    page += 1
```

### Pattern 5: JS-Rendered Pages (Playwright)
### Pattern 5: JS-Rendered Pages (Playwright)
### 模式 5：JS 渲染页面（Playwright）

```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    browser = p.chromium.launch()
    page = browser.new_page()
    page.goto(url)
    page.wait_for_selector(".listing")
    html = page.content()
    browser.close()

soup = BeautifulSoup(html, "lxml")
```

---

## Anti-Patterns to Avoid
## Anti-Patterns to Avoid
## 应避免的反模式

| Anti-pattern | Problem | Fix |
|---|---|---|
| One LLM call per item | Hits rate limits instantly | Batch 5 items per call |
| 每个项目一次 LLM 调用 | 立即达到速率限制 | 每调用批量 5 个项目 |
| Hardcoded keywords in code | Not reusable | Move all config to `config.yaml` |
| 代码中硬编码关键词 | 不可重用 | 将所有配置移到 `config.yaml` |
| Scraping without rate limit | IP ban | Add `time.sleep(1)` between requests |
| 无速率限制抓取 | IP 封禁 | 在请求之间添加 `time.sleep(1)` |
| Storing secrets in code | Security risk | Always use `.env` + GitHub Secrets |
| 在代码中存储密钥 | 安全风险 | 始终使用 `.env` + GitHub Secrets |
| No deduplication | Duplicate rows pile up | Always check URL before pushing |
| 无去重 | 重复行堆积 | 在推送前始终检查 URL |
| Ignoring `robots.txt` | Legal/ethical risk | Respect crawl rules; use public APIs when available |
| 忽略 `robots.txt` | 法律/伦理风险 | 尊重爬行规则；可用时使用公共 API |
| JS-rendered sites with `requests` | Empty response | Use Playwright or look for the underlying API |
| 对 JS 渲染站点使用 `requests` | 空响应 | 使用 Playwright 或寻找底层 API |
| `maxOutputTokens` too low | Truncated JSON, parse error | Use 2048+ for batch responses |
| `maxOutputTokens` 太低 | JSON 截断、解析错误 | 对批量响应使用 2048+ |

---

## Free Tier Limits Reference
## Free Tier Limits Reference
## 免费层级限制参考

| Service | Free Limit | Typical Usage |
|---|---|---|
| Gemini Flash Lite | 30 RPM, 1500 RPD | ~56 req/day at 3-hr intervals |
| Gemini Flash Lite | 30 RPM, 1500 RPD | ~56 请求/天（3 小时间隔） |
| Gemini 2.0 Flash | 15 RPM, 1500 RPD | Good fallback |
| Gemini 2.0 Flash | 15 RPM, 1500 RPD | 良好的回退 |
| Gemini 2.5 Flash | 10 RPM, 500 RPD | Use sparingly |
| Gemini 2.5 Flash | 10 RPM, 500 RPD | 谨慎使用 |
| GitHub Actions | Unlimited (public repos) | ~20 min/day |
| GitHub Actions | 无限（公共仓库） | ~20 分钟/天 |
| Notion API | Unlimited | ~200 writes/day |
| Notion API | 无限 | ~200 次写入/天 |
| Supabase | 500MB DB, 2GB transfer | Fine for most agents |
| Supabase | 500MB 数据库，2GB 传输 | 对大多数 agent 来说足够 |
| Google Sheets API | 300 req/min | Works for small agents |
| Google Sheets API | 300 请求/分钟 | 适用于小型 agent |

---

## Requirements Template
## Requirements Template
## 需求模板

```
requests==2.31.0
beautifulsoup4==4.12.3
lxml==5.1.0
python-dotenv==1.0.1
pyyaml==6.0.2
notion-client==2.2.1   # if using Notion
# playwright==1.40.0   # uncomment for JS-rendered sites
```

---

## Quality Checklist
## Quality Checklist
## 质量清单

Before marking the agent complete:
## Before marking the agent complete.
## 在标记 agent 完成之前：

- [ ] `config.yaml` controls all user-facing settings — no hardcoded values
  - [ ] `config.yaml` 控制所有面向用户的设置——没有硬编码值
- [ ] `profile/context.md` holds user-specific context for AI matching
  - [ ] `profile/context.md` 保存用于 AI 匹配的用户特定上下文
- [ ] Deduplication by URL before every storage push
  - [ ] 每次存储推送前按 URL 去重
- [ ] Gemini client has model fallback chain (4 models)
  - [ ] Gemini 客户端有模型回退链（4 个模型）
- [ ] Batch size ≤ 5 items per API call
  - [ ] 批量大小 ≤ 每 API 调用 5 个项目
- [ ] `maxOutputTokens` ≥ 2048
  - [ ] `maxOutputTokens` ≥ 2048
- [ ] `.env` is in `.gitignore`
  - [ ] `.env` 在 `.gitignore` 中
- [ ] `.env.example` provided for onboarding
  - [ ] 提供 `.env.example` 以便入门
- [ ] `setup.py` creates DB schema on first run
  - [ ] `setup.py` 在首次运行时创建 DB 模式
- [ ] `enrich_existing.py` backfills AI scores on old rows
  - [ ] `enrich_existing.py` 在旧行上回填 AI 分数
- [ ] GitHub Actions workflow commits `feedback.json` after each run
  - [ ] GitHub Actions 工作流在每次运行后提交 `feedback.json`
- [ ] README covers: setup in < 5 minutes, required secrets, customisation
  - [ ] README 涵盖：5 分钟内完成设置、所需密钥、自定义

---

## Real-World Examples
## Real-World Examples
## 真实世界示例

```
"Build me an agent that monitors Hacker News for AI startup funding news"
"构建一个监控 Hacker News 上 AI 初创公司融资新闻的 agent"
"Scrape product prices from 3 e-commerce sites and alert when they drop"
"从 3 个电子商务网站抓取产品价格并在降价时警报"
"Track new GitHub repos tagged with 'llm' or 'agents' — summarise each one"
"跟踪带有'llm'或'agents'标签的新 GitHub 仓库——总结每一个"
"Collect Chief of Staff job listings from LinkedIn and Cutshort into Notion"
"将 LinkedIn 和 Cutshort 的首席员工职位列表收集到 Notion"
"Monitor a subreddit for posts mentioning my company — classify sentiment"
"监控 subreddit 中提及我公司的帖子——分类情感"
"Scrape new academic papers from arXiv on a topic I care about daily"
"每天抓取 arXiv 上我关心主题的新学术论文"
"Track sports fixture results and keep a running table in Google Sheets"
"跟踪体育赛事结果并在 Google Sheets 中保持动态表格"
"Build a real estate listing watcher — alert on new properties under ₹1 Cr"
"构建房产列表观察器——在新房产低于 1 Cr 时警报"
```

---

## Reference Implementation
## Reference Implementation
## 参考实现

A complete working agent built with this exact architecture would scrape 4+ sources,
batch Gemini calls, learn from Applied/Rejected decisions stored in Notion, and run
100% free on GitHub Actions. Follow Steps 1-9 above to build your own.
## A complete working agent built with this exact architecture would scrape 4+ sources,
batch Gemini calls, learn from Applied/Rejected decisions stored in Notion, and run
100% free on GitHub Actions. Follow Steps 1-9 above to build your own.
## 使用此确切架构构建的完整工作 agent 将抓取 4+ 个来源，批量调用 Gemini，
从存储在 Notion 中的 Applied/Rejected 决策中学习，并在 GitHub Actions 上
100% 免费运行。按照上面的步骤 1-9 构建你自己的。
