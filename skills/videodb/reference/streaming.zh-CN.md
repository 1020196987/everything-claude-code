# Streaming & Playback

## Streaming & Playback
## 流传输和播放

VideoDB generates streams on-demand, returning HLS-compatible URLs that play instantly in any standard video player. No render times or export waits - edits, searches, and compositions stream immediately.
VideoDB 按需生成流，返回可在任何标准视频播放器中即时播放的 HLS 兼容 URL。无需渲染时间或导出等待——编辑、搜索和组合立即流式传输。

## Prerequisites

## Prerequisites
## 前提条件

Videos **must be uploaded** to a collection before streams can be generated. For search-based streams, the video must also be **indexed** (spoken words and/or scenes). See [search.md](search.md) for indexing details.
视频**必须上传**到 collection 中才能生成流。对于基于搜索的流，视频还必须**已索引**（口语和/或场景）。有关索引详情，参见 [search.md](search.md)。

## Core Concepts

## Core Concepts
## 核心概念

### Stream Generation

### Stream Generation
### 流生成

Every video, search result, and timeline in VideoDB can produce a **stream URL**. This URL points to an HLS (HTTP Live Streaming) manifest that is compiled on demand.
VideoDB 中的每个视频、搜索结果和时间线都可以生成**流 URL**。此 URL 指向按需编译的 HLS（HTTP Live Streaming）清单。

```python
# From a video
stream_url = video.generate_stream()

# From a timeline
stream_url = timeline.generate_stream()

# From search results
stream_url = results.compile()
```

## Streaming a Single Video

## Streaming a Single Video
## 流式传输单个视频

### Basic Playback

### Basic Playback
### 基本播放

```python
import videodb

conn = videodb.connect()
coll = conn.get_collection()
video = coll.get_video("your-video-id")

# Generate stream URL
stream_url = video.generate_stream()
print(f"Stream: {stream_url}")

# Open in default browser
video.play()
```

### With Subtitles

### With Subtitles
### 带字幕

```python
# Index and add subtitles first
video.index_spoken_words(force=True)
stream_url = video.add_subtitle()

# Returned URL already includes subtitles
print(f"Subtitled stream: {stream_url}")
```

### Specific Segments

### Specific Segments
### 特定片段

Stream only a portion of a video by passing a timeline of timestamp ranges:
通过传递时间戳范围的时间线来仅流式传输视频的一部分：

```python
# Stream seconds 10-30 and 60-90
stream_url = video.generate_stream(timeline=[(10, 30), (60, 90)])
print(f"Segment stream: {stream_url}")
```

## Streaming Timeline Compositions

## Streaming Timeline Compositions
## 流式传输时间线组合

Build a multi-asset composition and stream it in real time:
构建多资产组合并实时流式传输：

```python
import videodb
from videodb.timeline import Timeline
from videodb.asset import VideoAsset, AudioAsset, ImageAsset, TextAsset, TextStyle

conn = videodb.connect()
coll = conn.get_collection()

video = coll.get_video(video_id)
music = coll.get_audio(music_id)

timeline = Timeline(conn)

# Main video content
timeline.add_inline(VideoAsset(asset_id=video.id))

# Background music overlay (starts at second 0)
timeline.add_overlay(0, AudioAsset(asset_id=music.id))

# Text overlay at the beginning
timeline.add_overlay(0, TextAsset(
    text="Live Demo",
    duration=3,
    style=TextStyle(fontsize=48, fontcolor="white", boxcolor="#000000"),
))

# Generate the composed stream
stream_url = timeline.generate_stream()
print(f"Composed stream: {stream_url}")
```

**Important:** `add_inline()` only accepts `VideoAsset`. Use `add_overlay()` for `AudioAsset`, `ImageAsset`, and `TextAsset`.
**重要：** `add_inline()` 仅接受 `VideoAsset`。对 `AudioAsset`、`ImageAsset` 和 `TextAsset` 使用 `add_overlay()`。

For detailed timeline editing, see [editor.md](editor.md).
有关详细的时间线编辑，参见 [editor.md](editor.md)。

## Streaming Search Results

## Streaming Search Results
## 流式传输搜索结果

Compile search results into a single stream of all matching segments:
将搜索结果编译成所有匹配片段的单个流：

```python
from videodb import SearchType
from videodb.exceptions import InvalidRequestError

video.index_spoken_words(force=True)
try:
    results = video.search("key announcement", search_type=SearchType.semantic)

    # Compile all matching shots into one stream
    stream_url = results.compile()
    print(f"Search results stream: {stream_url}")

    # Or play directly
    results.play()
except InvalidRequestError as exc:
    if "No results found" in str(exc):
        print("No matching announcement segments were found.")
    else:
        raise
```

### Stream Individual Search Hits

### Stream Individual Search Hits
### 流式传输单个搜索命中

```python
from videodb.exceptions import InvalidRequestError

try:
    results = video.search("product demo", search_type=SearchType.semantic)
    for i, shot in enumerate(results.get_shots()):
        stream_url = shot.generate_stream()
        print(f"Hit {i+1} [{shot.start:.1f}s-{shot.end:.1f}s]: {stream_url}")
except InvalidRequestError as exc:
    if "No results found" in str(exc):
        print("No product demo segments matched the query.")
    else:
        raise
```

## Audio Playback

## Audio Playback
## 音频播放

Get a signed playback URL for audio content:
获取音频内容的签名播放 URL：

```python
audio = coll.get_audio(audio_id)
playback_url = audio.generate_url()
print(f"Audio URL: {playback_url}")
```

## Complete Workflow Examples

## Complete Workflow Examples
## 完整工作流示例

### Search-to-Stream Pipeline

### Search-to-Stream Pipeline
### 搜索到流管道

Combine search, timeline composition, and streaming in one workflow:
在一个工作流中组合搜索、时间线组合和流式传输：

```python
import videodb
from videodb import SearchType
from videodb.exceptions import InvalidRequestError
from videodb.timeline import Timeline
from videodb.asset import VideoAsset, TextAsset, TextStyle

conn = videodb.connect()
coll = conn.get_collection()
video = coll.get_video("your-video-id")

video.index_spoken_words(force=True)

# Search for key moments
queries = ["introduction", "main demo", "Q&A"]
timeline = Timeline(conn)
timeline_offset = 0.0

for query in queries:
    try:
        results = video.search(query, search_type=SearchType.semantic)
        shots = results.get_shots()
    except InvalidRequestError as exc:
        if "No results found" in str(exc):
            shots = []
        else:
            raise

    if not shots:
        continue

    # Add the section label where this batch starts in the compiled timeline
    timeline.add_overlay(timeline_offset, TextAsset(
        text=query.title(),
        duration=2,
        style=TextStyle(fontsize=36, fontcolor="white", boxcolor="#222222"),
    ))

    for shot in shots:
        timeline.add_inline(
            VideoAsset(asset_id=shot.video_id, start=shot.start, end=shot.end)
        )
        timeline_offset += shot.end - shot.start

stream_url = timeline.generate_stream()
print(f"Dynamic compilation: {stream_url}")
```

### Multi-Video Stream

### Multi-Video Stream
### 多视频流

Combine clips from different videos into a single stream:
将不同视频的片段组合成单个流：

```python
import videodb
from videodb.timeline import Timeline
from videodb.asset import VideoAsset

conn = videodb.connect()
coll = conn.get_collection()

video_clips = [
    {"id": "vid_001", "start": 0, "end": 15},
    {"id": "vid_002", "start": 10, "end": 30},
    {"id": "vid_003", "start": 5, "end": 25},
]

timeline = Timeline(conn)
for clip in video_clips:
    timeline.add_inline(
        VideoAsset(asset_id=clip["id"], start=clip["start"], end=clip["end"])
    )

stream_url = timeline.generate_stream()
print(f"Multi-video stream: {stream_url}")
```

### Conditional Stream Assembly

### Conditional Stream Assembly
### 条件流组装

Build a stream dynamically based on search availability:
基于搜索可用性动态构建流：

```python
import videodb
from videodb import SearchType
from videodb.exceptions import InvalidRequestError
from videodb.timeline import Timeline
from videodb.asset import VideoAsset, TextAsset, TextStyle

conn = videodb.connect()
coll = conn.get_collection()
video = coll.get_video("your-video-id")

video.index_spoken_words(force=True)

timeline = Timeline(conn)

# Try to find specific content; fall back to full video
topics = ["opening remarks", "technical deep dive", "closing"]

found_any = False
timeline_offset = 0.0
for topic in topics:
    try:
        results = video.search(topic, search_type=SearchType.semantic)
        shots = results.get_shots()
    except InvalidRequestError as exc:
        if "No results found" in str(exc):
            shots = []
        else:
            raise

    if shots:
        found_any = True
        timeline.add_overlay(timeline_offset, TextAsset(
            text=topic.title(),
            duration=2,
            style=TextStyle(fontsize=32, fontcolor="white", boxcolor="#1a1a2e"),
        ))
        for shot in shots:
            timeline.add_inline(
                VideoAsset(asset_id=shot.video_id, start=shot.start, end=shot.end)
            )
            timeline_offset += shot.end - shot.start

if found_any:
    stream_url = timeline.generate_stream()
    print(f"Curated stream: {stream_url}")
else:
    # Fall back to full video stream
    stream_url = video.generate_stream()
    print(f"Full video stream: {stream_url}")
```

### Live Event Recap

### Live Event Recap
### 直播活动回顾

Process an event recording into a streamable recap with multiple sections:
将活动录制处理成具有多个部分的可流式回顾：

```python
import videodb
from videodb import SearchType
from videodb.exceptions import InvalidRequestError
from videodb.timeline import Timeline
from videodb.asset import VideoAsset, AudioAsset, ImageAsset, TextAsset, TextStyle

conn = videodb.connect()
coll = conn.get_collection()

# Upload event recording
event = coll.upload(url="https://example.com/event-recording.mp4")
event.index_spoken_words(force=True)

# Generate background music
music = coll.generate_music(
    prompt="upbeat corporate background music",
    duration=120,
)

# Generate title image
title_img = coll.generate_image(
    prompt="modern event recap title card, dark background, professional",
    aspect_ratio="16:9",
)

# Build the recap timeline
timeline = Timeline(conn)
timeline_offset = 0.0

# Main video segments from search
try:
    keynote = event.search("keynote announcement", search_type=SearchType.semantic)
    keynote_shots = keynote.get_shots()[:5]
except InvalidRequestError as exc:
    if "No results found" in str(exc):
        keynote_shots = []
    else:
        raise
if keynote_shots:
    keynote_start = timeline_offset
    for shot in keynote_shots:
        timeline.add_inline(
            VideoAsset(asset_id=shot.video_id, start=shot.start, end=shot.end)
        )
        timeline_offset += shot.end - shot.start
else:
    keynote_start = None

try:
    demo = event.search("product demo", search_type=SearchType.semantic)
    demo_shots = demo.get_shots()[:5]
except InvalidRequestError as exc:
    if "No results found" in str(exc):
        demo_shots = []
    else:
        raise
if demo_shots:
    demo_start = timeline_offset
    for shot in demo_shots:
        timeline.add_inline(
            VideoAsset(asset_id=shot.video_id, start=shot.start, end=shot.end)
        )
        timeline_offset += shot.end - shot.start
else:
    demo_start = None

# Overlay title card image
timeline.add_overlay(0, ImageAsset(
    asset_id=title_img.id, width=100, height=100, x=80, y=20, duration=5
))

# Overlay section labels at the correct timeline offsets
if keynote_start is not None:
    timeline.add_overlay(max(5, keynote_start), TextAsset(
        text="Keynote Highlights",
        duration=3,
        style=TextStyle(fontsize=40, fontcolor="white", boxcolor="#0d1117"),
    ))
if demo_start is not None:
    timeline.add_overlay(max(5, demo_start), TextAsset(
        text="Demo Highlights",
        duration=3,
        style=TextStyle(fontsize=36, fontcolor="white", boxcolor="#0d1117"),
    ))

# Overlay background music
timeline.add_overlay(0, AudioAsset(
    asset_id=music.id, fade_in_duration=3
))

# Stream the final recap
stream_url = timeline.generate_stream()
print(f"Event recap: {stream_url}")
```

---

## Tips

## Tips
## 提示

- **HLS compatibility**: Stream URLs return HLS manifests (`.m3u8`). They work in Safari natively, and in other browsers via hls.js or similar libraries.
  - **HLS 兼容性**：流 URL 返回 HLS 清单（`.m3u8`）。它们在 Safari 中原生工作，并通过 hls.js 或类似库在其他浏览器中工作。
- **On-demand compilation**: Streams are compiled server-side when requested. The first play may have a brief compilation delay; subsequent plays of the same composition are cached.
  - **按需编译**：流在请求时在服务器端编译。第一次播放可能有短暂的编译延迟；相同组合的后续播放会被缓存。
- **Caching**: Calling `video.generate_stream()` a second time without arguments returns the cached stream URL rather than recompiling.
  - **缓存**：第二次调用 `video.generate_stream()` 且不带参数会返回缓存的流 URL 而不是重新编译。
- **Segment streams**: `video.generate_stream(timeline=[(start, end)])` is the fastest way to stream a specific clip without building a full `Timeline` object.
  - **片段流**：`video.generate_stream(timeline=[(start, end)])` 是在不构建完整 `Timeline` 对象的情况下流式传输特定片段的最快方式。
- **Inline vs overlay**: `add_inline()` only accepts `VideoAsset` and places assets sequentially on the main track. `add_overlay()` accepts `AudioAsset`, `ImageAsset`, and `TextAsset` and layers them on top at a given start time.
  - **内联与叠加**：`add_inline()` 仅接受 `VideoAsset` 并按顺序将资产放置在主轨上。`add_overlay()` 接受 `AudioAsset`、`ImageAsset` 和 `TextAsset`，并在给定开始时间将它们分层叠加在上方。
- **TextStyle defaults**: `TextStyle` defaults to `font='Sans'`, `fontcolor='black'`. Use `boxcolor` (not `bgcolor`) for background color on text.
  - **TextStyle 默认值**：`TextStyle` 默认为 `font='Sans'`、`fontcolor='black'`。对文字背景颜色使用 `boxcolor`（而非 `bgcolor`）。
- **Combine with generation**: Use `coll.generate_music(prompt, duration)` and `coll.generate_image(prompt, aspect_ratio)` to create assets for timeline compositions.
  - **与生成结合**：使用 `coll.generate_music(prompt, duration)` 和 `coll.generate_image(prompt, aspect_ratio)` 为时间线组合创建资产。
- **Playback**: `.play()` opens the stream URL in the default system browser. For programmatic use, work with the URL string directly.
  - **播放**：`.play()` 在默认系统浏览器中打开流 URL。对于程序化使用，直接使用 URL 字符串。
