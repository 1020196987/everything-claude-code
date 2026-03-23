# RTStream Reference

## RTStream Reference
## RTStream 参考

Code-level details for RTStream operations. For workflow guide, see [rtstream.md](rtstream.md).
RTStream 操作的代码级细节。工作流指南，参见 [rtstream.md](rtstream.md)。

For usage guidance and workflow selection, start with [../SKILL.md](../SKILL.md).
有关使用指导和流程选择，从 [../SKILL.md](../SKILL.md) 开始。

Based on [docs.videodb.io](https://docs.videodb.io/pages/ingest/live-streams/realtime-apis.md).
基于 [docs.videodb.io](https://docs.videodb.io/pages/ingest/live-streams/realtime-apis.md)。

---

## Collection RTStream Methods

## Collection RTStream Methods
## Collection RTStream 方法

Methods on `Collection` for managing RTStreams:
`Collection` 上用于管理 RTStream 的方法：

| Method | Returns | Description |
|--------|---------|-------------|
| `coll.connect_rtstream(url, name, ...)` | `RTStream` | Create new RTStream from RTSP/RTMP URL |
| `coll.connect_rtstream(url, name, ...)` | `RTStream` | 从 RTSP/RTMP URL 创建新 RTStream |
| `coll.get_rtstream(id)` | `RTStream` | Get existing RTStream by ID |
| `coll.get_rtstream(id)` | `RTStream` | 通过 ID 获取现有 RTStream |
| `coll.list_rtstreams(limit, offset, status, name, ordering)` | `List[RTStream]` | List all RTStreams in collection |
| `coll.list_rtstreams(limit, offset, status, name, ordering)` | `List[RTStream]` | 列出 collection 中的所有 RTStream |
| `coll.search(query, namespace="rtstream")` | `RTStreamSearchResult` | Search across all RTStreams |
| `coll.search(query, namespace="rtstream")` | `RTStreamSearchResult` | 在所有 RTStream 中搜索 |

### Connect RTStream

### Connect RTStream
### 连接 RTStream

```python
import videodb

conn = videodb.connect()
coll = conn.get_collection()

rtstream = coll.connect_rtstream(
    url="rtmp://your-stream-server/live/stream-key",
    name="My Live Stream",
    media_types=["video"],  # or ["audio", "video"]
    sample_rate=30,         # optional
    store=True,             # enable recording storage for export
    enable_transcript=True, # optional
    ws_connection_id=ws_id, # optional, for real-time events
)
```

### Get Existing RTStream

### Get Existing RTStream
### 获取现有 RTStream

```python
rtstream = coll.get_rtstream("rts-xxx")
```

### List RTStreams

### List RTStreams
### 列出 RTStream

```python
rtstreams = coll.list_rtstreams(
    limit=10,
    offset=0,
    status="connected",  # optional filter
    name="meeting",      # optional filter
    ordering="-created_at",
)

for rts in rtstreams:
    print(f"{rts.id}: {rts.name} - {rts.status}")
```

### From Capture Session

### From Capture Session
### 从捕获会话

After a capture session is active, retrieve RTStream objects:
捕获会话激活后，获取 RTStream 对象：

```python
session = conn.get_capture_session(session_id)

mics = session.get_rtstream("mic")
displays = session.get_rtstream("screen")
system_audios = session.get_rtstream("system_audio")
```

Or use the `rtstreams` data from the `capture_session.active` WebSocket event:
或使用 `capture_session.active` WebSocket 事件中的 `rtstreams` 数据：

```python
for rts in rtstreams:
    rtstream = coll.get_rtstream(rts["rtstream_id"])
```

---

## RTStream Methods

## RTStream Methods
## RTStream 方法

| Method | Returns | Description |
|--------|---------|-------------|
| `rtstream.start()` | `None` | Begin ingestion |
| `rtstream.start()` | `None` | 开始摄入 |
| `rtstream.stop()` | `None` | Stop ingestion |
| `rtstream.stop()` | `None` | 停止摄入 |
| `rtstream.generate_stream(start, end)` | `str` | Stream recorded segment (Unix timestamps) |
| `rtstream.generate_stream(start, end)` | `str` | 流式传输录制的片段（Unix 时间戳） |
| `rtstream.export(name=None)` | `RTStreamExportResult` | Export to permanent video |
| `rtstream.export(name=None)` | `RTStreamExportResult` | 导出为永久视频 |
| `rtstream.index_visuals(prompt, ...)` | `RTStreamSceneIndex` | Create visual index with AI analysis |
| `rtstream.index_visuals(prompt, ...)` | `RTStreamSceneIndex` | 使用 AI 分析创建视觉索引 |
| `rtstream.index_audio(prompt, ...)` | `RTStreamSceneIndex` | Create audio index with LLM summarization |
| `rtstream.index_audio(prompt, ...)` | `RTStreamSceneIndex` | 使用 LLM 摘要创建音频索引 |
| `rtstream.list_scene_indexes()` | `List[RTStreamSceneIndex]` | List all scene indexes on the stream |
| `rtstream.list_scene_indexes()` | `List[RTStreamSceneIndex]` | 列出流上的所有场景索引 |
| `rtstream.get_scene_index(index_id)` | `RTStreamSceneIndex` | Get a specific scene index |
| `rtstream.get_scene_index(index_id)` | `RTStreamSceneIndex` | 获取特定场景索引 |
| `rtstream.search(query, ...)` | `RTStreamSearchResult` | Search indexed content |
| `rtstream.search(query, ...)` | `RTStreamSearchResult` | 搜索索引内容 |
| `rtstream.start_transcript(ws_connection_id, engine)` | `dict` | Start live transcription |
| `rtstream.start_transcript(ws_connection_id, engine)` | `dict` | 开始实时转录 |
| `rtstream.get_transcript(page, page_size, start, end, since)` | `dict` | Get transcript pages |
| `rtstream.get_transcript(page, page_size, start, end, since)` | `dict` | 获取转录页面 |
| `rtstream.stop_transcript(engine)` | `dict` | Stop transcription |
| `rtstream.stop_transcript(engine)` | `dict` | 停止转录 |

---

## Starting and Stopping

## Starting and Stopping
## 启动和停止

```python
# Begin ingestion
rtstream.start()

# ... stream is being recorded ...

# Stop ingestion
rtstream.stop()
```

---

## Generating Streams

## Generating Streams
## 生成流

Use Unix timestamps (not seconds offsets) to generate a playback stream from recorded content:
使用 Unix 时间戳（而非秒偏移）从录制内容生成播放流：

```python
import time

start_ts = time.time()
rtstream.start()

# Let it record for a while...
time.sleep(60)

end_ts = time.time()
rtstream.stop()

# Generate a stream URL for the recorded segment
stream_url = rtstream.generate_stream(start=start_ts, end=end_ts)
print(f"Recorded stream: {stream_url}")
```

---

## Exporting to Video

## Exporting to Video
## 导出为视频

Export the recorded stream to a permanent video in the collection:
将录制的流导出为 collection 中的永久视频：

```python
export_result = rtstream.export(name="Meeting Recording 2024-01-15")

print(f"Video ID: {export_result.video_id}")
print(f"Stream URL: {export_result.stream_url}")
print(f"Player URL: {export_result.player_url}")
print(f"Duration: {export_result.duration}s")
```

### RTStreamExportResult Properties

### RTStreamExportResult Properties
### RTStreamExportResult 属性

| Property | Type | Description |
|----------|------|-------------|
| `video_id` | `str` | ID of the exported video |
| `video_id` | `str` | 导出视频的 ID |
| `stream_url` | `str` | HLS stream URL |
| `stream_url` | `str` | HLS 流 URL |
| `player_url` | `str` | Web player URL |
| `player_url` | `str` | Web 播放器 URL |
| `name` | `str` | Video name |
| `name` | `str` | 视频名称 |
| `duration` | `float` | Duration in seconds |
| `duration` | `float` | 时长（秒） |

---

## AI Pipelines

## AI Pipelines
## AI 管道

AI pipelines process live streams and send results via WebSocket.
AI 管道处理直播流并通过 WebSocket 发送结果。

### RTStream AI Pipeline Methods

### RTStream AI Pipeline Methods
### RTStream AI 管道方法

| Method | Returns | Description |
|--------|---------|-------------|
| `rtstream.index_audio(prompt, batch_config, ...)` | `RTStreamSceneIndex` | Start audio indexing with LLM summarization |
| `rtstream.index_audio(prompt, batch_config, ...)` | `RTStreamSceneIndex` | 使用 LLM 摘要启动音频索引 |
| `rtstream.index_visuals(prompt, batch_config, ...)` | `RTStreamSceneIndex` | Start visual indexing of screen content |
| `rtstream.index_visuals(prompt, batch_config, ...)` | `RTStreamSceneIndex` | 启动屏幕内容的视觉索引 |

### Audio Indexing

### Audio Indexing
### 音频索引

Generate LLM summaries of audio content at intervals:
按间隔生成音频内容的 LLM 摘要：

```python
audio_index = rtstream.index_audio(
    prompt="Summarize what is being discussed",
    batch_config={"type": "word", "value": 50},
    model_name=None,       # optional
    name="meeting_audio",  # optional
    ws_connection_id=ws_id,
)
```

**Audio batch_config options:**
**音频 batch_config 选项：**

| Type | Value | Description |
|------|-------|-------------|
| `"word"` | count | Segment every N words |
| `"word"` | count | 每 N 个词分段 |
| `"sentence"` | count | Segment every N sentences |
| `"sentence"` | count | 每 N 个句子分段 |
| `"time"` | seconds | Segment every N seconds |
| `"time"` | seconds | 每 N 秒分段 |

Examples:
示例：

```python
{"type": "word", "value": 50}      # every 50 words
{"type": "sentence", "value": 5}   # every 5 sentences
{"type": "time", "value": 30}      # every 30 seconds
```

Results arrive on the `audio_index` WebSocket channel.
结果到达 `audio_index` WebSocket 通道。

### Visual Indexing

### Visual Indexing
### 视觉索引

Generate AI descriptions of visual content:
生成视觉内容的 AI 描述：

```python
scene_index = rtstream.index_visuals(
    prompt="Describe what is happening on screen",
    batch_config={"type": "time", "value": 2, "frame_count": 5},
    model_name="basic",
    name="screen_monitor",  # optional
    ws_connection_id=ws_id,
)
```

**Parameters:**
**参数：**

| Parameter | Type | Description |
|-----------|------|-------------|
| `prompt` | `str` | Instructions for the AI model (supports structured JSON output) |
| `prompt` | `str` | AI 模型的指令（支持结构化 JSON 输出） |
| `batch_config` | `dict` | Controls frame sampling (see below) |
| `batch_config` | `dict` | 控制帧采样（见下文） |
| `model_name` | `str` | Model tier: `"mini"`, `"basic"`, `"pro"`, `"ultra"` |
| `model_name` | `str` | 模型层级：`"mini"`、`"basic"`、`"pro"`、`"ultra"` |
| `name` | `str` | Name for the index (optional) |
| `name` | `str` | 索引名称（可选） |
| `ws_connection_id` | `str` | WebSocket connection ID for receiving results |
| `ws_connection_id` | `str` | 用于接收结果的 WebSocket 连接 ID |

**Visual batch_config:**
**视觉 batch_config：**

| Key | Type | Description |
|-----|------|-------------|
| `type` | `str` | Only `"time"` is supported for visuals |
| `type` | `str` | 视觉仅支持 `"time"` |
| `value` | `int` | Window size in seconds |
| `value` | `int` | 窗口大小（秒） |
| `frame_count` | `int` | Number of frames to extract per window |
| `frame_count` | `int` | 每个窗口提取的帧数 |

Example: `{"type": "time", "value": 2, "frame_count": 5}` samples 5 frames every 2 seconds and sends them to the model.
示例：`{"type": "time", "value": 2, "frame_count": 5}` 每 2 秒采样 5 帧并将其发送到模型。

**Structured JSON output:**
**结构化 JSON 输出：**

Use a prompt that requests JSON format for structured responses:
使用请求 JSON 格式的提示以获取结构化响应：

```python
scene_index = rtstream.index_visuals(
    prompt="""Analyze the screen and return a JSON object with:
{
  "app_name": "name of the active application",
  "activity": "what the user is doing",
  "ui_elements": ["list of visible UI elements"],
  "contains_text": true/false,
  "dominant_colors": ["list of main colors"]
}
Return only valid JSON.""",
    batch_config={"type": "time", "value": 3, "frame_count": 3},
    model_name="pro",
    ws_connection_id=ws_id,
)
```

Results arrive on the `scene_index` WebSocket channel.
结果到达 `scene_index` WebSocket 通道。

---

## Batch Config Summary

## Batch Config Summary
## 批处理配置摘要

| Indexing Type | `type` Options | `value` | Extra Keys |
|---------------|----------------|---------|------------|
| **Audio** | `"word"`, `"sentence"`, `"time"` | words/sentences/seconds | - |
| **Audio** | `"word"`、`"sentence"`、`"time"` | 词/句子/秒 | - |
| **Visual** | `"time"` only | seconds | `frame_count` |
| **Visual** | 仅 `"time"` | 秒 | `frame_count` |

Examples:
示例：

```python
# Audio: every 50 words
{"type": "word", "value": 50}

# Audio: every 30 seconds
{"type": "time", "value": 30}

# Visual: 5 frames every 2 seconds
{"type": "time", "value": 2, "frame_count": 5}
```

---

## Transcription

## Transcription
## 转录

Real-time transcription via WebSocket:
通过 WebSocket 进行实时转录：

```python
# Start live transcription
rtstream.start_transcript(
    ws_connection_id=ws_id,
    engine=None,  # optional, defaults to "assemblyai"
)

# Get transcript pages (with optional filters)
transcript = rtstream.get_transcript(
    page=1,
    page_size=100,
    start=None,   # optional: start timestamp filter
    end=None,     # optional: end timestamp filter
    since=None,   # optional: for polling, get transcripts after this timestamp
    engine=None,
)

# Stop transcription
rtstream.stop_transcript(engine=None)
```

Transcript results arrive on the `transcript` WebSocket channel.
转录结果到达 `transcript` WebSocket 通道。

---

## RTStreamSceneIndex

## RTStreamSceneIndex
## RTStreamSceneIndex

When you call `index_audio()` or `index_visuals()`, the method returns an `RTStreamSceneIndex` object. This object represents the running index and provides methods for managing scenes and alerts.
当您调用 `index_audio()` 或 `index_visuals()` 时，该方法返回 `RTStreamSceneIndex` 对象。此对象表示正在运行的索引，并提供用于管理场景和警报的方法。

```python
# index_visuals returns an RTStreamSceneIndex
scene_index = rtstream.index_visuals(
    prompt="Describe what is on screen",
    ws_connection_id=ws_id,
)

# index_audio also returns an RTStreamSceneIndex
audio_index = rtstream.index_audio(
    prompt="Summarize the discussion",
    ws_connection_id=ws_id,
)
```

### RTStreamSceneIndex Properties

### RTStreamSceneIndex Properties
### RTStreamSceneIndex 属性

| Property | Type | Description |
|----------|------|-------------|
| `rtstream_index_id` | `str` | Unique ID of the index |
| `rtstream_index_id` | `str` | 索引的唯一 ID |
| `rtstream_id` | `str` | ID of the parent RTStream |
| `rtstream_id` | `str` | 父 RTStream 的 ID |
| `extraction_type` | `str` | Type of extraction (`time` or `transcript`) |
| `extraction_type` | `str` | 提取类型（`time` 或 `transcript`） |
| `extraction_config` | `dict` | Extraction configuration |
| `extraction_config` | `dict` | 提取配置 |
| `prompt` | `str` | The prompt used for analysis |
| `prompt` | `str` | 用于分析的提示 |
| `name` | `str` | Name of the index |
| `name` | `str` | 索引名称 |
| `status` | `str` | Status (`connected`, `stopped`) |
| `status` | `str` | 状态（`connected`、`stopped`） |

### RTStreamSceneIndex Methods

### RTStreamSceneIndex Methods
### RTStreamSceneIndex 方法

| Method | Returns | Description |
|--------|---------|-------------|
| `index.get_scenes(start, end, page, page_size)` | `dict` | Get indexed scenes |
| `index.get_scenes(start, end, page, page_size)` | `dict` | 获取索引场景 |
| `index.start()` | `None` | Start/resume the index |
| `index.start()` | `None` | 启动/恢复索引 |
| `index.stop()` | `None` | Stop the index |
| `index.stop()` | `None` | 停止索引 |
| `index.create_alert(event_id, callback_url, ws_connection_id)` | `str` | Create alert for event detection |
| `index.create_alert(event_id, callback_url, ws_connection_id)` | `str` | 为事件检测创建警报 |
| `index.list_alerts()` | `list` | List all alerts on this index |
| `index.list_alerts()` | `list` | 列出此索引上的所有警报 |
| `index.enable_alert(alert_id)` | `None` | Enable an alert |
| `index.enable_alert(alert_id)` | `None` | 启用警报 |
| `index.disable_alert(alert_id)` | `None` | Disable an alert |
| `index.disable_alert(alert_id)` | `None` | 禁用警报 |

### Getting Scenes

### Getting Scenes
### 获取场景

Poll indexed scenes from the index:
从索引中轮询索引场景：

```python
result = scene_index.get_scenes(
    start=None,      # optional: start timestamp
    end=None,        # optional: end timestamp
    page=1,
    page_size=100,
)

for scene in result["scenes"]:
    print(f"[{scene['start']}-{scene['end']}] {scene['text']}")

if result["next_page"]:
    # fetch next page
    pass
```

### Managing Scene Indexes

### Managing Scene Indexes
### 管理场景索引

```python
# List all indexes on the stream
indexes = rtstream.list_scene_indexes()

# Get a specific index by ID
scene_index = rtstream.get_scene_index(index_id)

# Stop an index
scene_index.stop()

# Restart an index
scene_index.start()
```

---

## Events

## Events
## 事件

Events are reusable detection rules. Create them once, attach to any index via alerts.
事件是可重用的检测规则。创建一次，通过警报附加到任何索引。

### Connection Event Methods

### Connection Event Methods
### 连接事件方法

| Method | Returns | Description |
|--------|---------|-------------|
| `conn.create_event(event_prompt, label)` | `str` (event_id) | Create detection event |
| `conn.create_event(event_prompt, label)` | `str` (event_id) | 创建检测事件 |
| `conn.list_events()` | `list` | List all events |
| `conn.list_events()` | `list` | 列出所有事件 |

### Creating an Event

### Creating an Event
### 创建事件

```python
event_id = conn.create_event(
    event_prompt="User opened Slack application",
    label="slack_opened",
)
```

### Listing Events

### Listing Events
### 列出事件

```python
events = conn.list_events()
for event in events:
    print(f"{event['event_id']}: {event['label']}")
```

---

## Alerts

## Alerts
## 警报

Alerts wire events to indexes for real-time notifications. When the AI detects content matching the event description, an alert is sent.
警报将事件连接到索引以进行实时通知。当 AI 检测到与事件描述匹配的内容时，会发送警报。

### Creating an Alert

### Creating an Alert
### 创建警报

```python
# Get the RTStreamSceneIndex from index_visuals
scene_index = rtstream.index_visuals(
    prompt="Describe what application is open on screen",
    ws_connection_id=ws_id,
)

# Create an alert on the index
alert_id = scene_index.create_alert(
    event_id=event_id,
    callback_url="https://your-backend.com/alerts",  # for webhook delivery
    ws_connection_id=ws_id,  # for WebSocket delivery (optional)
)
```

**Note:** `callback_url` is required. Pass an empty string `""` if only using WebSocket delivery.
**注意：** `callback_url` 是必需的。如果仅使用 WebSocket 传递，传入空字符串 `""`。

### Managing Alerts

### Managing Alerts
### 管理警报

```python
# List all alerts on an index
alerts = scene_index.list_alerts()

# Enable/disable alerts
scene_index.disable_alert(alert_id)
scene_index.enable_alert(alert_id)
```

### Alert Delivery

### Alert Delivery
### 警报传递

| Method | Latency | Use Case |
|--------|---------|----------|
| WebSocket | Real-time | Dashboards, live UI |
| Webhook | < 1 second | Server-to-server, automation |
| | | |
| 方法 | 延迟 | 用例 |
| WebSocket | 实时 | 仪表板、实时 UI |
| Webhook | < 1 秒 | 服务器到服务器、自动化 |

### WebSocket Alert Event

### WebSocket Alert Event
### WebSocket 警报事件

```json
{
  "channel": "alert",
  "rtstream_id": "rts-xxx",
  "data": {
    "event_label": "slack_opened",
    "timestamp": 1710000012340,
    "text": "User opened Slack application"
  }
}
```

### Webhook Payload

### Webhook Payload
### Webhook 载荷

```json
{
  "event_id": "event-xxx",
  "label": "slack_opened",
  "confidence": 0.95,
  "explanation": "User opened the Slack application",
  "timestamp": "2024-01-15T10:30:45Z",
  "start_time": 1234.5,
  "end_time": 1238.0,
  "stream_url": "https://stream.videodb.io/v3/...",
  "player_url": "https://console.videodb.io/player?url=..."
}
```

---

## WebSocket Integration

## WebSocket Integration
## WebSocket 集成

All real-time AI results are delivered via WebSocket. Pass `ws_connection_id` to:
所有实时 AI 结果通过 WebSocket 传递。将 `ws_connection_id` 传递给：

- `rtstream.start_transcript()`
- `rtstream.index_audio()`
- `rtstream.index_visuals()`
- `scene_index.create_alert()`

### WebSocket Channels

### WebSocket Channels
### WebSocket 通道

| Channel | Source | Content |
|---------|--------|---------|
| `transcript` | `start_transcript()` | Real-time speech-to-text |
| `transcript` | `start_transcript()` | 实时语音转文本 |
| `scene_index` | `index_visuals()` | Visual analysis results |
| `scene_index` | `index_visuals()` | 视觉分析结果 |
| `audio_index` | `index_audio()` | Audio analysis results |
| `audio_index` | `index_audio()` | 音频分析结果 |
| `alert` | `create_alert()` | Alert notifications |
| `alert` | `create_alert()` | 警报通知 |

For WebSocket event structures and ws_listener usage, see [capture-reference.md](capture-reference.md).
有关 WebSocket 事件结构和 ws_listener 用法，参见 [capture-reference.md](capture-reference.md)。

---

## Complete Workflow

## Complete Workflow
## 完整工作流

```python
import time
import videodb
from videodb.exceptions import InvalidRequestError

conn = videodb.connect()
coll = conn.get_collection()

# 1. Connect and start recording
rtstream = coll.connect_rtstream(
    url="rtmp://your-stream-server/live/stream-key",
    name="Weekly Standup",
    store=True,
)
rtstream.start()

# 2. Record for the duration of the meeting
start_ts = time.time()
time.sleep(1800)  # 30 minutes
end_ts = time.time()
rtstream.stop()

# Generate an immediate playback URL for the captured window
stream_url = rtstream.generate_stream(start=start_ts, end=end_ts)
print(f"Recorded stream: {stream_url}")

# 3. Export to a permanent video
export_result = rtstream.export(name="Weekly Standup Recording")
print(f"Exported video: {export_result.video_id}")

# 4. Index the exported video for search
video = coll.get_video(export_result.video_id)
video.index_spoken_words(force=True)

# 5. Search for action items
try:
    results = video.search("action items and next steps")
    stream_url = results.compile()
    print(f"Action items clip: {stream_url}")
except InvalidRequestError as exc:
    if "No results found" in str(exc):
        print("No action items were detected in the recording.")
    else:
        raise
```
