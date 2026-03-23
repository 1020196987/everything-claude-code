---
name: videodb
description: See, Understand, Act on video and audio. See- ingest from local files, URLs, RTSP/live feeds, or live record desktop; return realtime context and playable stream links. Understand- extract frames, build visual/semantic/temporal indexes, and search moments with timestamps and auto-clips. Act- transcode and normalize (codec, fps, resolution, aspect ratio), perform timeline edits (subtitles, text/image overlays, branding, audio overlays, dubbing, translation), generate media assets (image, audio, video), and create real time alerts for events from live streams or desktop capture.
description zh-CN: 看、理解、行动于视频和音频。看 — 从本地文件、URL、RTSP/直播流或实时录制桌面采集；返回实时上下文和可播放流链接。理解 — 提取帧，构建视觉/语义/时间索引，并按时间戳和自动剪辑搜索时刻。行动 — 转码和标准化（编解码器、FPS、分辨率、宽高比），执行时间线编辑（字幕、文本/图像叠加、品牌、音频叠加、配音、翻译），生成媒体素材（图像、音频、视频），以及从直播流或桌面采集创建实时事件警报。
origin: ECC
allowed-tools: Read Grep Glob Bash(python:*)
argument-hint: "[task description]"
---

# VideoDB Skill

## VideoDB Skill
## VideoDB 技能

**Perception + memory + actions for video, live streams, and desktop sessions.**

**用于视频、直播和桌面会话的感知 + 记忆 + 行动。**

## When to use

## When to use
## 何时使用

### Desktop Perception
### Desktop Perception
### 桌面感知

- Start/stop a **desktop session** capturing **screen, mic, and system audio**
  - 开始/停止**桌面会话**，采集**屏幕、麦克风和系统音频**
- Stream **live context** and store **episodic session memory**
  - 流式传输**实时上下文**并存储**情景会话记忆**
- Run **real-time alerts/triggers** on what's spoken and what's happening on screen
  - 对所说内容和屏幕上发生的事情运行**实时警报/触发器**
- Produce **session summaries**, a searchable timeline, and **playable evidence links**
  - 生成**会话摘要**、可搜索时间线和**可播放证据链接**

### Video ingest + stream
### Video ingest + stream
### 视频摄入 + 流

- Ingest a **file or URL** and return a **playable web stream link**
  - 摄入**文件或 URL** 并返回**可播放网络流链接**
- Transcode/normalize: **codec, bitrate, fps, resolution, aspect ratio**
  - 转码/标准化：**编解码器、比特率、FPS、分辨率、宽高比**

### Index + search (timestamps + evidence)
### Index + search (timestamps + evidence)
### 索引 + 搜索（时间戳 + 证据）

- Build **visual**, **spoken**, and **keyword** indexes
  - 构建**视觉**、**口语**和**关键词**索引
- Search and return exact moments with **timestamps** and **playable evidence**
  - 搜索并返回带**时间戳**和**可播放证据**的精确时刻
- Auto-create **clips** from search results
  - 从搜索结果自动创建**剪辑**

### Timeline editing + generation
### Timeline editing + generation
### 时间线编辑 + 生成

- Subtitles: **generate**, **translate**, **burn-in**
  - 字幕：**生成**、**翻译**、**烧录**
- Overlays: **text/image/branding**, motion captions
  - 叠加层：**文本/图像/品牌**、动态字幕
- Audio: **background music**, **voiceover**, **dubbing**
  - 音频：**背景音乐**、**配音**、**配音**
- Programmatic composition and exports via **timeline operations**
  - 通过**时间线操作**进行编程合成和导出

### Live streams (RTSP) + monitoring
### Live streams (RTSP) + monitoring
### 直播流（RTSP）+ 监控

- Connect **RTSP/live feeds**
  - 连接 **RTSP/直播流**
- Run **real-time visual and spoken understanding** and emit **events/alerts** for monitoring workflows
  - 运行**实时视觉和口语理解**并为监控工作流发出**事件/警报**

## How it works

## How it works
## 工作原理

### Common inputs
### Common inputs
### 常见输入

- Local **file path**, public **URL**, or **RTSP URL**
  - 本地**文件路径**、公共**URL** 或 **RTSP URL**
- Desktop capture request: **start / stop / summarize session**
  - 桌面采集请求：**开始 / 停止 / 总结会话**
- Desired operations: get context for understanding, transcode spec, index spec, search query, clip ranges, timeline edits, alert rules
  - 期望操作：获取理解上下文、转码规格、索引规格、搜索查询、剪辑范围、时间线编辑、警报规则

### Common outputs
### Common outputs
### 常见输出

- **Stream URL**
  - **流 URL**
- Search results with **timestamps** and **evidence links**
  - 带**时间戳**和**证据链接**的搜索结果
- Generated assets: subtitles, audio, images, clips
  - 生成的素材：字幕、音频、图像、剪辑
- **Event/alert payloads** for live streams
  - 直播流的**事件/警报载荷**
- Desktop **session summaries** and memory entries
  - 桌面**会话摘要**和记忆条目

### Running Python code

### Running Python code
### 运行 Python 代码

Before running any VideoDB code, change to the project directory and load environment variables:

在运行任何 VideoDB 代码之前，切换到项目目录并加载环境变量：

```python
from dotenv import load_dotenv
load_dotenv(".env")

import videodb
conn = videodb.connect()
```

This reads `VIDEO_DB_API_KEY` from:
1. Environment (if already exported)
2. Project's `.env` file in current directory

这从以下位置读取 `VIDEO_DB_API_KEY`：
1. 环境（如果已导出）
2. 当前目录中项目的 `.env` 文件

If the key is missing, `videodb.connect()` raises `AuthenticationError` automatically.

如果密钥缺失，`videodb.connect()` 自动引发 `AuthenticationError`。

Do NOT write a script file when a short inline command works.

当简短的内联命令可行时，不要写脚本文件。

When writing inline Python (`python -c "..."`), always use properly formatted code — use semicolons to separate statements and keep it readable. For anything longer than ~3 statements, use a heredoc instead:

编写内联 Python（`python -c "..."`）时，始终使用正确格式的代码 — 用分号分隔语句并保持可读。对于超过约 3 条语句的任何内容，改用 heredoc：

```bash
python << 'EOF'
from dotenv import load_dotenv
load_dotenv(".env")

import videodb
conn = videodb.connect()
coll = conn.get_collection()
print(f"Videos: {len(coll.get_videos())}")
EOF
```

### Setup

### Setup
### 设置

When the user asks to "setup videodb" or similar:

当用户要求"设置 videodb"或类似时：

### 1. Install SDK

### 1. Install SDK
### 1. 安装 SDK

```bash
pip install "videodb[capture]" python-dotenv
```

If `videodb[capture]` fails on Linux, install without the capture extra:

如果 `videodb[capture]` 在 Linux 上失败，安装不带 capture extra 的版本：

```bash
pip install videodb python-dotenv
```

### 2. Configure API key

### 2. Configure API key
### 2. 配置 API 密钥

The user must set `VIDEO_DB_API_KEY` using **either** method:

用户必须使用**以下任一**方法设置 `VIDEO_DB_API_KEY`：

- **Export in terminal** (before starting Claude): `export VIDEO_DB_API_KEY=your-key`
  - **在终端中导出**（启动 Claude 前）：`export VIDEO_DB_API_KEY=your-key`
- **Project `.env` file**: Save `VIDEO_DB_API_KEY=your-key` in the project's `.env` file
  - **项目 `.env` 文件**：在项目的 `.env` 文件中保存 `VIDEO_DB_API_KEY=your-key`

Get a free API key at [console.videodb.io](https://console.videodb.io) (50 free uploads, no credit card).

在 [console.videodb.io](https://console.videodb.io) 获取免费 API 密钥（50 次免费上传，无需信用卡）。

**Do NOT** read, write, or handle the API key yourself. Always let the user set it.

**不要**自己读取、写入或处理 API 密钥。始终让用户设置它。

### Quick Reference

### Quick Reference
### 快速参考

### Upload media

### Upload media
### 上传媒体

```python
# URL
video = coll.upload(url="https://example.com/video.mp4")

# YouTube
video = coll.upload(url="https://www.youtube.com/watch?v=VIDEO_ID")

# Local file
video = coll.upload(file_path="/path/to/video.mp4")
```

### Transcript + subtitle

### Transcript + subtitle
### 转录 + 字幕

```python
# force=True skips the error if the video is already indexed
video.index_spoken_words(force=True)
text = video.get_transcript_text()
stream_url = video.add_subtitle()
```

### Search inside videos

### Search inside videos
### 在视频内搜索

```python
from videodb.exceptions import InvalidRequestError

video.index_spoken_words(force=True)

# search() raises InvalidRequestError when no results are found.
# Always wrap in try/except and treat "No results found" as empty.
try:
    results = video.search("product demo")
    shots = results.get_shots()
    stream_url = results.compile()
except InvalidRequestError as e:
    if "No results found" in str(e):
        shots = []
    else:
        raise
```

### Scene search

### Scene search
### 场景搜索

```python
import re
from videodb import SearchType, IndexType, SceneExtractionType
from videodb.exceptions import InvalidRequestError

# index_scenes() has no force parameter — it raises an error if a scene
# index already exists. Extract the existing index ID from the error.
try:
    scene_index_id = video.index_scenes(
        extraction_type=SceneExtractionType.shot_based,
        prompt="Describe the visual content in this scene.",
    )
except Exception as e:
    match = re.search(r"id\s+([a-f0-9]+)", str(e))
    if match:
        scene_index_id = match.group(1)
    else:
        raise

# Use score_threshold to filter low-relevance noise (recommended: 0.3+)
try:
    results = video.search(
        query="person writing on a whiteboard",
        search_type=SearchType.semantic,
        index_type=IndexType.scene,
        scene_index_id=scene_index_id,
        score_threshold=0.3,
    )
    shots = results.get_shots()
    stream_url = results.compile()
except InvalidRequestError as e:
    if "No results found" in str(e):
        shots = []
    else:
        raise
```

### Timeline editing

### Timeline editing
### 时间线编辑

**Important:** Always validate timestamps before building a timeline:
**重要：** 在构建时间线前始终验证时间戳：

- `start` must be >= 0 (negative values are silently accepted but produce broken output)
  - `start` 必须 >= 0（负值会被静默接受但产生损坏输出）
- `start` must be < `end`
  - `start` 必须 < `end`
- `end` must be <= `video.length`
  - `end` 必须 <= `video.length`

```python
from videodb.timeline import Timeline
from videodb.asset import VideoAsset, TextAsset, TextStyle

timeline = Timeline(conn)
timeline.add_inline(VideoAsset(asset_id=video.id, start=10, end=30))
timeline.add_overlay(0, TextAsset(text="The End", duration=3, style=TextStyle(fontsize=36)))
stream_url = timeline.generate_stream()
```

### Transcode video (resolution / quality change)

### Transcode video (resolution / quality change)
### 转码视频（分辨率 / 质量变化）

```python
from videodb import TranscodeMode, VideoConfig, AudioConfig

# Change resolution, quality, or aspect ratio server-side
job_id = conn.transcode(
    source="https://example.com/video.mp4",
    callback_url="https://example.com/webhook",
    mode=TranscodeMode.economy,
    video_config=VideoConfig(resolution=720, quality=23, aspect_ratio="16:9"),
    audio_config=AudioConfig(mute=False),
)
```

### Reframe aspect ratio (for social platforms)

### Reframe aspect ratio (for social platforms)
### 重新构图宽高比（用于社交平台）

**Warning:** `reframe()` is a slow server-side operation. For long videos it can take several minutes and may time out. Best practices:
**警告：** `reframe()` 是一个慢速服务器端操作。对于长视频可能需要几分钟且可能超时。最佳实践：

- Always limit to a short segment using `start`/`end` when possible
  - 尽可能使用 `start`/`end` 限制为短片段
- For full-length videos, use `callback_url` for async processing
  - 对于完整视频，使用 `callback_url` 进行异步处理
- Trim the video on a `Timeline` first, then reframe the shorter result
  - 先在 `Timeline` 上修剪视频，然后重新构图较短的结果

```python
from videodb import ReframeMode

# Always prefer reframing a short segment:
reframed = video.reframe(start=0, end=60, target="vertical", mode=ReframeMode.smart)

# Async reframe for full-length videos (returns None, result via webhook):
video.reframe(target="vertical", callback_url="https://example.com/webhook")

# Presets: "vertical" (9:16), "square" (1:1), "landscape" (16:9)
reframed = video.reframe(start=0, end=60, target="square")

# Custom dimensions
reframed = video.reframe(start=0, end=60, target={"width": 1280, "height": 720})
```

### Generative media

### Generative media
### 生成媒体

```python
image = coll.generate_image(
    prompt="a sunset over mountains",
    aspect_ratio="16:9",
)
```

## Error handling

## Error handling
## 错误处理

```python
from videodb.exceptions import AuthenticationError, InvalidRequestError

try:
    conn = videodb.connect()
except AuthenticationError:
    print("Check your VIDEO_DB_API_KEY")

try:
    video = coll.upload(url="https://example.com/video.mp4")
except InvalidRequestError as e:
    print(f"Upload failed: {e}")
```

### Common pitfalls

### Common pitfalls
### 常见陷阱

| Scenario | Error message | Solution | 场景 | 错误消息 | 解决方案 |
|----------|--------------|----------|------|----------|----------|
| Indexing an already-indexed video | `Spoken word index for video already exists` | Use `video.index_spoken_words(force=True)` to skip if already indexed | 索引已索引的视频 | `Spoken word index for video already exists` | 使用 `video.index_spoken_words(force=True)` 在已索引时跳过 |
| Scene index already exists | `Scene index with id XXXX already exists` | Extract the existing `scene_index_id` from the error with `re.search(r"id\s+([a-f0-9]+)", str(e))` | 场景索引已存在 | `Scene index with id XXXX already exists` | 用 `re.search(r"id\s+([a-f0-9]+)", str(e))` 从错误中提取现有的 `scene_index_id` |
| Search finds no matches | `InvalidRequestError: No results found` | Catch the exception and treat as empty results (`shots = []`) | 搜索无匹配 | `InvalidRequestError: No results found` | 捕获异常并视为空结果（`shots = []`） |
| Reframe times out | Blocks indefinitely on long videos | Use `start`/`end` to limit segment, or pass `callback_url` for async | 重新构图文超时 | 在长视频上无限阻塞 | 使用 `start`/`end` 限制片段，或传递 `callback_url` 进行异步 |
| Negative timestamps on Timeline | Silently produces broken stream | Always validate `start >= 0` before creating `VideoAsset` | 时间线上的负时间戳 | 静默产生损坏的流 | 在创建 `VideoAsset` 前始终验证 `start >= 0` |
| `generate_video()` / `create_collection()` fails | `Operation not allowed` or `maximum limit` | Plan-gated features — inform the user about plan limits | `generate_video()` / `create_collection()` 失败 | `Operation not allowed` 或 `maximum limit` | 计划限制功能 — 告知用户计划限制 |

## Examples

## Examples
## 示例

### Canonical prompts
### Canonical prompts
### 规范提示

- "Start desktop capture and alert when a password field appears."
  - "开始桌面采集并在出现密码字段时警报。"
- "Record my session and produce an actionable summary when it ends."
  - "录制我的会话并在结束时生成可操作的摘要。"
- "Ingest this file and return a playable stream link."
  - "摄入此文件并返回可播放流链接。"
- "Index this folder and find every scene with people, return timestamps."
  - "索引此文件夹并查找每个有人的场景，返回时间戳。"
- "Generate subtitles, burn them in, and add light background music."
  - "生成字幕，烧录进去，并添加轻量背景音乐。"
- "Connect this RTSP URL and alert when a person enters the zone."
  - "连接此 RTSP URL 并在有人进入区域时警报。"

### Screen Recording (Desktop Capture)

### Screen Recording (Desktop Capture)
### 屏幕录制（桌面采集）

Use `ws_listener.py` to capture WebSocket events during recording sessions. Desktop capture supports **macOS** only.

使用 `ws_listener.py` 在录制会话期间采集 WebSocket 事件。桌面采集仅支持 **macOS**。

#### Quick Start

#### Quick Start
#### 快速开始

1. **Choose state dir**: `STATE_DIR="${VIDEODB_EVENTS_DIR:-$HOME/.local/state/videodb}"`
   - **选择状态目录**：`STATE_DIR="${VIDEODB_EVENTS_DIR:-$HOME/.local/state/videodb}"`
2. **Start listener**: `VIDEODB_EVENTS_DIR="$STATE_DIR" python scripts/ws_listener.py --clear "$STATE_DIR" &`
   - **启动监听器**：`VIDEODB_EVENTS_DIR="$STATE_DIR" python scripts/ws_listener.py --clear "$STATE_DIR" &`
3. **Get WebSocket ID**: `cat "$STATE_DIR/videodb_ws_id"`
   - **获取 WebSocket ID**：`cat "$STATE_DIR/videodb_ws_id"`
4. **Run capture code** (see reference/capture.md for the full workflow)
   - **运行采集代码**（完整工作流见 reference/capture.md）
5. **Events written to**: `$STATE_DIR/videodb_events.jsonl`
   - **事件写入到**：`$STATE_DIR/videodb_events.jsonl`

Use `--clear` whenever you start a fresh capture run so stale transcript and visual events do not leak into the new session.

每当开始新的采集运行时使用 `--clear`，以避免陈旧的转录和视觉事件泄漏到新会话中。

#### Query Events

#### Query Events
#### 查询事件

```python
import json
import os
import time
from pathlib import Path

events_dir = Path(os.environ.get("VIDEODB_EVENTS_DIR", Path.home() / ".local" / "state" / "videodb"))
events_file = events_dir / "videodb_events.jsonl"
events = []

if events_file.exists():
    with events_file.open(encoding="utf-8") as handle:
        for line in handle:
            try:
                events.append(json.loads(line))
            except json.JSONDecodeError:
                continue

transcripts = [e["data"]["text"] for e in events if e.get("channel") == "transcript"]
cutoff = time.time() - 300
recent_visual = [
    e for e in events
    if e.get("channel") == "visual_index" and e["unix_ts"] > cutoff
]
```

## Additional docs

## Additional docs
## 附加文档

Reference documentation is in the `reference/` directory adjacent to this SKILL.md file. Use the Glob tool to locate it if needed.

参考文档在此 SKILL.md 文件相邻的 `reference/` 目录中。如有需要使用 Glob 工具定位。

- [reference/api-reference.md](reference/api-reference.md) - Complete VideoDB Python SDK API reference
  - [reference/api-reference.md](reference/api-reference.md) - 完整的 VideoDB Python SDK API 参考
- [reference/search.md](reference/search.md) - In-depth guide to video search (spoken word and scene-based)
  - [reference/search.md](reference/search.md) - 视频搜索深入指南（口语和基于场景）
- [reference/editor.md](reference/editor.md) - Timeline editing, assets, and composition
  - [reference/editor.md](reference/editor.md) - 时间线编辑、素材和合成
- [reference/streaming.md](reference/streaming.md) - HLS streaming and instant playback
  - [reference/streaming.md](reference/streaming.md) - HLS 流媒体和即时播放
- [reference/generative.md](reference/generative.md) - AI-powered media generation (images, video, audio)
  - [reference/generative.md](reference/generative.md) - AI 驱动的媒体生成（图像、视频、音频）
- [reference/rtstream.md](reference/rtstream.md) - Live stream ingestion workflow (RTSP/RTMP)
  - [reference/rtstream.md](reference/rtstream.md) - 直播流摄入工作流（RTSP/RTMP）
- [reference/rtstream-reference.md](reference/rtstream-reference.md) - RTStream SDK methods and AI pipelines
  - [reference/rtstream-reference.md](reference/rtstream-reference.md) - RTStream SDK 方法和 AI 管道
- [reference/capture.md](reference/capture.md) - Desktop capture workflow
  - [reference/capture.md](reference/capture.md) - 桌面采集工作流
- [reference/capture-reference.md](reference/capture-reference.md) - Capture SDK and WebSocket events
  - [reference/capture-reference.md](reference/capture-reference.md) - Capture SDK 和 WebSocket 事件
- [reference/use-cases.md](reference/use-cases.md) - Common video processing patterns and examples
  - [reference/use-cases.md](reference/use-cases.md) - 常见视频处理模式和示例

**Do not use ffmpeg, moviepy, or local encoding tools** when VideoDB supports the operation. The following are all handled server-side by VideoDB — trimming, combining clips, overlaying audio or music, adding subtitles, text/image overlays, transcoding, resolution changes, aspect-ratio conversion, resizing for platform requirements, transcription, and media generation. Only fall back to local tools for operations listed under Limitations in reference/editor.md (transitions, speed changes, crop/zoom, colour grading, volume mixing).

**当 VideoDB 支持该操作时不要使用 ffmpeg、moviepy 或本地编码工具。** 以下所有操作都由 VideoDB 服务器端处理 — 修剪、组合剪辑、叠加音频或音乐、添加字幕、文本/图像叠加、转码、分辨率更改、宽高比转换、平台要求的大小调整、转录和媒体生成。仅对 reference/editor.md 中限制下列出的操作使用本地工具（转场、速度更改、裁剪/缩放、颜色分级、音量混合）。

### When to use what

### When to use what
### 何时使用什么

| Problem | VideoDB solution | 问题 | VideoDB 解决方案 |
|---------|----------------|------|-----------------|
| Platform rejects video aspect ratio or resolution | `video.reframe()` or `conn.transcode()` with `VideoConfig` | 平台拒绝视频宽高比或分辨率 | `video.reframe()` 或带 `VideoConfig` 的 `conn.transcode()` |
| Need to resize video for Twitter/Instagram/TikTok | `video.reframe(target="vertical")` or `target="square"` | 需要为 Twitter/Instagram/TikTok 调整视频大小 | `video.reframe(target="vertical")` 或 `target="square"` |
| Need to change resolution (e.g. 1080p → 720p) | `conn.transcode()` with `VideoConfig(resolution=720)` | 需要更改分辨率（如 1080p → 720p） | 带 `VideoConfig(resolution=720)` 的 `conn.transcode()` |
| Need to overlay audio/music on video | `AudioAsset` on a `Timeline` | 需要在视频上叠加音频/音乐 | 在 `Timeline` 上的 `AudioAsset` |
| Need to add subtitles | `video.add_subtitle()` or `CaptionAsset` | 需要添加字幕 | `video.add_subtitle()` 或 `CaptionAsset` |
| Need to combine/trim clips | `VideoAsset` on a `Timeline` | 需要组合/修剪剪辑 | 在 `Timeline` 上的 `VideoAsset` |
| Need to generate voiceover, music, or SFX | `coll.generate_voice()`, `generate_music()`, `generate_sound_effect()` | 需要生成配音、音乐或音效 | `coll.generate_voice()`、`generate_music()`、`generate_sound_effect()` |

## Provenance

## Provenance
## 来源

Reference material for this skill is vendored locally under `skills/videodb/reference/`.
Use the local copies above instead of following external repository links at runtime.

此 skill 的参考材料本地保存在 `skills/videodb/reference/` 下。
使用上面的本地副本，而非在运行时跟随外部仓库链接。

**Maintained By:** [VideoDB](https://www.videodb.io/)
