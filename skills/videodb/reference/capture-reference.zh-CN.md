# Capture Reference

## Capture Reference
## 捕获参考

Code-level details for VideoDB capture sessions. For workflow guide, see [capture.md](capture.md).
VideoDB 捕获会话的代码级细节。工作流指南，参见 [capture.md](capture.md)。

---

## WebSocket Events

## WebSocket Events
## WebSocket 事件

Real-time events from capture sessions and AI pipelines. No webhooks or polling required.
来自捕获会话和 AI 管道的实时事件。无需 webhook 或轮询。

Use [scripts/ws_listener.py](../scripts/ws_listener.py) to connect and dump events to `${VIDEODB_EVENTS_DIR:-$HOME/.local/state/videodb}/videodb_events.jsonl`.
使用 [scripts/ws_listener.py](../scripts/ws_listener.py) 连接并将事件转储到 `${VIDEODB_EVENTS_DIR:-$HOME/.local/state/videodb}/videodb_events.jsonl`。

### Event Channels

### Event Channels
### 事件通道

| Channel | Source | Content |
|---------|--------|---------|
| `capture_session` | Session lifecycle | Status changes |
| `capture_session` | 会话生命周期 | 状态变化 |
| `transcript` | `start_transcript()` | Speech-to-text |
| `transcript` | `start_transcript()` | 语音转文本 |
| `visual_index` / `scene_index` | `index_visuals()` | Visual analysis |
| `visual_index` / `scene_index` | `index_visuals()` | 视觉分析 |
| `audio_index` | `index_audio()` | Audio analysis |
| `audio_index` | `index_audio()` | 音频分析 |
| `alert` | `create_alert()` | Alert notifications |
| `alert` | `create_alert()` | 警报通知 |

### Session Lifecycle Events

### Session Lifecycle Events
### 会话生命周期事件

| Event | Status | Key Data |
|-------|--------|----------|
| `capture_session.created` | `created` | — |
| `capture_session.starting` | `starting` | — |
| `capture_session.active` | `active` | `rtstreams[]` |
| `capture_session.stopping` | `stopping` | — |
| `capture_session.stopped` | `stopped` | — |
| `capture_session.exported` | `exported` | `exported_video_id`, `stream_url`, `player_url` |
| `capture_session.failed` | `failed` | `error` |

### Event Structures

### Event Structures
### 事件结构

**Transcript event:**
**转录事件：**

```json
{
  "channel": "transcript",
  "rtstream_id": "rts-xxx",
  "rtstream_name": "mic:default",
  "data": {
    "text": "Let's schedule the meeting for Thursday",
    "is_final": true,
    "start": 1710000001234,
    "end": 1710000002345
  }
}
```

**Visual index event:**
**视觉索引事件：**

```json
{
  "channel": "visual_index",
  "rtstream_id": "rts-xxx",
  "rtstream_name": "display:1",
  "data": {
    "text": "User is viewing a Slack conversation with 3 unread messages",
    "start": 1710000012340,
    "end": 1710000018900
  }
}
```

**Audio index event:**
**音频索引事件：**

```json
{
  "channel": "audio_index",
  "rtstream_id": "rts-xxx",
  "rtstream_name": "mic:default",
  "data": {
    "text": "Discussion about scheduling a team meeting",
    "start": 1710000021500,
    "end": 1710000029200
  }
}
```

**Session active event:**
**会话激活事件：**

```json
{
  "event": "capture_session.active",
  "capture_session_id": "cap-xxx",
  "status": "active",
  "data": {
    "rtstreams": [
      { "rtstream_id": "rts-1", "name": "mic:default", "media_types": ["audio"] },
      { "rtstream_id": "rts-2", "name": "system_audio:default", "media_types": ["audio"] },
      { "rtstream_id": "rts-3", "name": "display:1", "media_types": ["video"] }
    ]
  }
}
```

**Session exported event:**
**会话导出事件：**

```json
{
  "event": "capture_session.exported",
  "capture_session_id": "cap-xxx",
  "status": "exported",
  "data": {
    "exported_video_id": "v_xyz789",
    "stream_url": "https://stream.videodb.io/...",
    "player_url": "https://console.videodb.io/player?url=..."
  }
}
```

> For latest details, see [VideoDB Realtime Context docs](https://docs.videodb.io/pages/ingest/capture-sdks/realtime-context.md).
> 最新详情，参见 [VideoDB Realtime Context 文档](https://docs.videodb.io/pages/ingest/capture-sdks/realtime-context.md)。

---

## Event Persistence

## Event Persistence
## 事件持久化

Use `ws_listener.py` to dump all WebSocket events to a JSONL file for later analysis.
使用 `ws_listener.py` 将所有 WebSocket 事件转储到 JSONL 文件以供后续分析。

### Start Listener and Get WebSocket ID

### Start Listener and Get WebSocket ID
### 启动监听器并获取 WebSocket ID

```bash
# Start with --clear to clear old events (recommended for new sessions)
# 使用 --clear 启动以清除旧事件（新会话推荐）
python scripts/ws_listener.py --clear &

# Append to existing events (for reconnects)
# 追加到现有事件（用于重新连接）
python scripts/ws_listener.py &
```

Or specify a custom output directory:
或指定自定义输出目录：

```bash
python scripts/ws_listener.py --clear /path/to/output &
# Or via environment variable:
# 或通过环境变量：
VIDEODB_EVENTS_DIR=/path/to/output python scripts/ws_listener.py --clear &
```

The script outputs `WS_ID=<connection_id>` on the first line, then listens indefinitely.
脚本在第一行输出 `WS_ID=<connection_id>`，然后无限监听。

**Get the ws_id:**
**获取 ws_id：**

```bash
cat "${VIDEODB_EVENTS_DIR:-$HOME/.local/state/videodb}/videodb_ws_id"
```

**Stop the listener:**
**停止监听器：**

```bash
kill "$(cat "${VIDEODB_EVENTS_DIR:-$HOME/.local/state/videodb}/videodb_ws_pid")"
```

**Functions that accept `ws_connection_id`:**
**接受 `ws_connection_id` 的函数：**

| Function | Purpose |
|----------|---------|
| `conn.create_capture_session()` | Session lifecycle events |
| `conn.create_capture_session()` | 会话生命周期事件 |
| RTStream methods | See [rtstream-reference.md](rtstream-reference.md) |
| RTStream 方法 | 参见 [rtstream-reference.md](rtstream-reference.md) |

**Output files** (in output directory, default `${XDG_STATE_HOME:-$HOME/.local/state}/videodb`):
**输出文件**（在输出目录中，默认为 `${XDG_STATE_HOME:-$HOME/.local/state}/videodb`）：

- `videodb_ws_id` - WebSocket connection ID
  - `videodb_ws_id` - WebSocket 连接 ID
- `videodb_events.jsonl` - All events
  - `videodb_events.jsonl` - 所有事件
- `videodb_ws_pid` - Process ID for easy termination
  - `videodb_ws_pid` - 用于轻松终止的进程 ID

**Features:**
**特性：**

- `--clear` flag to clear events file on start (use for new sessions)
  - `--clear` 标志在启动时清除事件文件（新会话使用）
- Auto-reconnect with exponential backoff on connection drops
  - 连接断开时使用指数退避自动重连
- Graceful shutdown on SIGINT/SIGTERM
  - SIGINT/SIGTERM 时优雅关闭
- Connection status logging
  - 连接状态日志

### JSONL Format

### JSONL Format
### JSONL 格式

Each line is a JSON object with added timestamps:
每行是一个带有附加时间戳的 JSON 对象：

```json
{"ts": "2026-03-02T10:15:30.123Z", "unix_ts": 1772446530.123, "channel": "visual_index", "data": {"text": "..."}}
{"ts": "2026-03-02T10:15:31.456Z", "unix_ts": 1772446531.456, "event": "capture_session.active", "capture_session_id": "cap-xxx"}
```

### Reading Events

### Reading Events
### 读取事件

```python
import json
import time
from pathlib import Path

events_path = Path.home() / ".local" / "state" / "videodb" / "videodb_events.jsonl"
transcripts = []
recent = []
visual = []

cutoff = time.time() - 600
with events_path.open(encoding="utf-8") as handle:
    for line in handle:
        event = json.loads(line)
        if event.get("channel") == "transcript":
            transcripts.append(event)
        if event.get("unix_ts", 0) > cutoff:
            recent.append(event)
        if (
            event.get("channel") == "visual_index"
            and "code" in event.get("data", {}).get("text", "").lower()
        ):
            visual.append(event)
```

---

## WebSocket Connection

## WebSocket Connection
## WebSocket 连接

Connect to receive real-time AI results from transcription and indexing pipelines.
连接以接收来自转录和索引管道的实时 AI 结果。

```python
ws_wrapper = conn.connect_websocket()
ws = await ws_wrapper.connect()
ws_id = ws.connection_id
```

| Property / Method | Type | Description |
|-------------------|------|-------------|
| `ws.connection_id` | `str` | Unique connection ID (pass to AI pipeline methods) |
| `ws.connection_id` | `str` | 唯一连接 ID（传递给 AI 管道方法） |
| `ws.receive()` | `AsyncIterator[dict]` | Async iterator yielding real-time messages |
| `ws.receive()` | `AsyncIterator[dict]` | 生成实时消息的异步迭代器 |

---

## CaptureSession

## CaptureSession
## 捕获会话

### Connection Methods

### Connection Methods
### 连接方法

| Method | Returns | Description |
|--------|---------|-------------|
| `conn.create_capture_session(end_user_id, collection_id, ws_connection_id, metadata)` | `CaptureSession` | Create a new capture session |
| `conn.create_capture_session(end_user_id, collection_id, ws_connection_id, metadata)` | `CaptureSession` | 创建新的捕获会话 |
| `conn.get_capture_session(capture_session_id)` | `CaptureSession` | Retrieve an existing capture session |
| `conn.get_capture_session(capture_session_id)` | `CaptureSession` | 获取现有捕获会话 |
| `conn.generate_client_token()` | `str` | Generate a client-side authentication token |
| `conn.generate_client_token()` | `str` | 生成客户端身份验证令牌 |

### Create a Capture Session

### Create a Capture Session
### 创建捕获会话

```python
from pathlib import Path

ws_id = (Path.home() / ".local" / "state" / "videodb" / "videodb_ws_id").read_text().strip()

session = conn.create_capture_session(
    end_user_id="user-123",  # required
    collection_id="default",
    ws_connection_id=ws_id,
    metadata={"app": "my-app"},
)
print(f"Session ID: {session.id}")
```

> **Note:** `end_user_id` is required and identifies the user initiating the capture. For testing or demo purposes, any unique string identifier works (e.g., `"demo-user"`, `"test-123"`).
> **注意：** `end_user_id` 是必需的，用于标识发起捕获的用户。对于测试或演示目的，任何唯一的字符串标识符都可以（例如 `"demo-user"`、`"test-123"`）。

### CaptureSession Properties

### CaptureSession Properties
### CaptureSession 属性

| Property | Type | Description |
|----------|------|-------------|
| `session.id` | `str` | Unique capture session ID |
| `session.id` | `str` | 唯一捕获会话 ID |

### CaptureSession Methods

### CaptureSession Methods
### CaptureSession 方法

| Method | Returns | Description |
|--------|---------|-------------|
| `session.get_rtstream(type)` | `list[RTStream]` | Get RTStreams by type: `"mic"`, `"screen"`, or `"system_audio"` |
| `session.get_rtstream(type)` | `list[RTStream]` | 按类型获取 RTStream：`"mic"`、`"screen"` 或 `"system_audio"` |

### Generate a Client Token

### Generate a Client Token
### 生成客户端令牌

```python
token = conn.generate_client_token()
```

---

## CaptureClient

## CaptureClient
## 捕获客户端

The client runs on the user's machine and handles permissions, channel discovery, and streaming.
客户端在用户机器上运行，处理权限、通道发现和流传输。

```python
from videodb.capture import CaptureClient

client = CaptureClient(client_token=token)
```

### CaptureClient Methods

### CaptureClient Methods
### CaptureClient 方法

| Method | Returns | Description |
|--------|---------|-------------|
| `await client.request_permission(type)` | `None` | Request device permission (`"microphone"`, `"screen_capture"`) |
| `await client.request_permission(type)` | `None` | 请求设备权限（`"microphone"`、`"screen_capture"`） |
| `await client.list_channels()` | `Channels` | Discover available audio/video channels |
| `await client.list_channels()` | `Channels` | 发现可用的音频/视频通道 |
| `await client.start_capture_session(capture_session_id, channels, primary_video_channel_id)` | `None` | Start streaming selected channels |
| `await client.start_capture_session(capture_session_id, channels, primary_video_channel_id)` | `None` | 开始流式传输选定的通道 |
| `await client.stop_capture()` | `None` | Gracefully stop the capture session |
| `await client.stop_capture()` | `None` | 优雅地停止捕获会话 |
| `await client.shutdown()` | `None` | Clean up client resources |
| `await client.shutdown()` | `None` | 清理客户端资源 |

### Request Permissions

### Request Permissions
### 请求权限

```python
await client.request_permission("microphone")
await client.request_permission("screen_capture")
```

### Start a Session

### Start a Session
### 启动会话

```python
selected_channels = [c for c in [mic, display, system_audio] if c]
await client.start_capture_session(
    capture_session_id=session.id,
    channels=selected_channels,
    primary_video_channel_id=display.id if display else None,
)
```

### Stop a Session

### Stop a Session
### 停止会话

```python
await client.stop_capture()
await client.shutdown()
```

---

## Channels

## Channels
## 通道

Returned by `client.list_channels()`. Groups available devices by type.
由 `client.list_channels()` 返回。按类型分组可用设备。

```python
channels = await client.list_channels()
for ch in channels.all():
    print(f"  {ch.id} ({ch.type}): {ch.name}")

mic = channels.mics.default
display = channels.displays.default
system_audio = channels.system_audio.default
```

### Channel Groups

### Channel Groups
### 通道组

| Property | Type | Description |
|----------|------|-------------|
| `channels.mics` | `ChannelGroup` | Available microphones |
| `channels.mics` | `ChannelGroup` | 可用麦克风 |
| `channels.displays` | `ChannelGroup` | Available screen displays |
| `channels.displays` | `ChannelGroup` | 可用屏幕显示器 |
| `channels.system_audio` | `ChannelGroup` | Available system audio sources |
| `channels.system_audio` | `ChannelGroup` | 可用系统音频源 |

### ChannelGroup Methods & Properties

### ChannelGroup Methods & Properties
### ChannelGroup 方法和属性

| Member | Type | Description |
|--------|------|-------------|
| `group.default` | `Channel` | Default channel in the group (or `None`) |
| `group.default` | `Channel` | 组中的默认通道（或 `None`） |
| `group.all()` | `list[Channel]` | All channels in the group |
| `group.all()` | `list[Channel]` | 组中的所有通道 |

### Channel Properties

### Channel Properties
### 通道属性

| Property | Type | Description |
|----------|------|-------------|
| `ch.id` | `str` | Unique channel ID |
| `ch.id` | `str` | 唯一通道 ID |
| `ch.type` | `str` | Channel type (`"mic"`, `"display"`, `"system_audio"`) |
| `ch.type` | `str` | 通道类型（`"mic"`、`"display"`、`"system_audio"`） |
| `ch.name` | `str` | Human-readable channel name |
| `ch.name` | `str` | 人类可读的通道名称 |
| `ch.store` | `bool` | Whether to persist the recording (set to `True` to save) |
| `ch.store` | `bool` | 是否持久化录制（设置为 `True` 以保存） |

Without `store = True`, streams are processed in real-time but not saved.
如果没有 `store = True`，流会实时处理但不会保存。

---

## RTStreams and AI Pipelines

## RTStreams and AI Pipelines
## RTStream 和 AI 管道

After session is active, retrieve RTStream objects with `session.get_rtstream()`.
会话激活后，使用 `session.get_rtstream()` 获取 RTStream 对象。

For RTStream methods (indexing, transcription, alerts, batch config), see [rtstream-reference.md](rtstream-reference.md).
关于 RTStream 方法（索引、转录、警报、批处理配置），参见 [rtstream-reference.md](rtstream-reference.md)。

---

## Session Lifecycle

## Session Lifecycle
## 会话生命周期

```
  create_capture_session()
          │
          v
  ┌───────────────┐
  │    created     │
  └───────┬───────┘
          │  client.start_capture_session()
          v
  ┌───────────────┐     WebSocket: capture_session.starting
  │   starting     │ ──> Capture channels connect
  └───────┬───────┘
          │
          v
  ┌───────────────┐     WebSocket: capture_session.active
  │    active      │ ──> Start AI pipelines
  └───────┬──────────────┐
          │              │
          │              v
          │      ┌───────────────┐     WebSocket: capture_session.failed
          │      │    failed      │ ──> Inspect error payload and retry setup
          │      └───────────────┘
          │      unrecoverable capture error
          │
          │  client.stop_capture()
          v
  ┌───────────────┐     WebSocket: capture_session.stopping
  │   stopping     │ ──> Finalize streams
  └───────┬───────┘
          │
          v
  ┌───────────────┐     WebSocket: capture_session.stopped
  │   stopped      │ ──> All streams finalized
  └───────┬───────┘
          │  (if store=True)
          v
  ┌───────────────┐     WebSocket: capture_session.exported
  │   exported     │ ──> Access video_id, stream_url, player_url
  └───────────────┘
```
