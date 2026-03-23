# Capture Guide

## Capture Guide
## 捕获指南

## Overview

## Overview
## 概述

VideoDB Capture enables real-time screen and audio recording with AI processing. Desktop capture currently supports **macOS** only.
VideoDB Capture 支持实时屏幕和音频录制以及 AI 处理。桌面捕获目前仅支持 **macOS**。

For code-level details (SDK methods, event structures, AI pipelines), see [capture-reference.md](capture-reference.md).
有关代码级细节（SDK 方法、事件结构、AI 管道），参见 [capture-reference.md](capture-reference.md)。

## Quick Start

## Quick Start
## 快速开始

1. **Start WebSocket listener**: `python scripts/ws_listener.py --clear &`
   1. **启动 WebSocket 监听器**：`python scripts/ws_listener.py --clear &`
2. **Run capture code** (see Complete Capture Workflow below)
   2. **运行捕获代码**（参见下面的完整捕获工作流）
3. **Events written to**: `/tmp/videodb_events.jsonl`
   3. **事件写入位置**：`/tmp/videodb_events.jsonl`

---

## Complete Capture Workflow

## Complete Capture Workflow
## 完整捕获工作流

No webhooks or polling required. WebSocket delivers all events including session lifecycle.
无需 webhook 或轮询。WebSocket 传递所有事件，包括会话生命周期。

> **CRITICAL:** The `CaptureClient` must remain running for the entire duration of the capture. It runs the local recorder binary that streams screen/audio data to VideoDB. If the Python process that created the `CaptureClient` exits, the recorder binary is killed and capture stops silently. Always run the capture code as a **long-lived background process** (e.g. `nohup python capture_script.py &`) and use signal handling (`asyncio.Event` + `SIGINT`/`SIGTERM`) to keep it alive until you explicitly stop it.
> **关键：** `CaptureClient` 必须在整个捕获期间保持运行。它运行本地录制器二进制文件，将屏幕/音频数据流式传输到 VideoDB。如果创建 `CaptureClient` 的 Python 进程退出，录制器二进制文件将被终止，捕获将静默停止。始终将捕获代码作为**长期后台进程**运行（例如 `nohup python capture_script.py &`），并使用信号处理（`asyncio.Event` + `SIGINT`/`SIGTERM`）保持其运行，直到您明确停止它。

1. **Start WebSocket listener** in background with `--clear` flag to clear old events. Wait for it to create the WebSocket ID file.
   1. **在后台启动 WebSocket 监听器**，使用 `--clear` 标志清除旧事件。等待它创建 WebSocket ID 文件。
2. **Read the WebSocket ID**. This ID is required for capture session and AI pipelines.
   2. **读取 WebSocket ID**。此 ID 是捕获会话和 AI 管道所必需的。
3. **Create a capture session** and generate a client token for the desktop client.
   3. **创建捕获会话**并为桌面客户端生成客户端令牌。
4. **Initialize CaptureClient** with the token. Request permissions for microphone and screen capture.
   4. **使用令牌初始化 CaptureClient**。请求麦克风和屏幕捕获权限。
5. **List and select channels** (mic, display, system_audio). Set `store = True` on channels you want to persist as a video.
   5. **列出并选择通道**（mic、display、system_audio）。在要持久化为视频的通道上设置 `store = True`。
6. **Start the session** with selected channels.
   6. **使用选定的通道启动会话**。
7. **Wait for session active** by reading events until you see `capture_session.active`. This event contains the `rtstreams` array. Save session info (session ID, RTStream IDs) to a file (e.g. `/tmp/videodb_capture_info.json`) so other scripts can read it.
   7. **等待会话激活**，通过读取事件直到看到 `capture_session.active`。此事件包含 `rtstreams` 数组。将会话信息（session ID、RTStream ID）保存到文件（例如 `/tmp/videodb_capture_info.json`），以便其他脚本可以读取。
8. **Keep the process alive.** Use `asyncio.Event` with signal handlers for `SIGINT`/`SIGTERM` to block until explicitly stopped. Write a PID file (e.g. `/tmp/videodb_capture_pid`) so the process can be stopped later with `kill $(cat /tmp/videodb_capture_pid)`. The PID file should be overwritten on every run so reruns always have the correct PID.
   8. **保持进程存活。** 使用 `asyncio.Event` 配合 `SIGINT`/`SIGTERM` 信号处理器来阻塞，直到明确停止。写入 PID 文件（例如 `/tmp/videodb_capture_pid`），以便稍后可以用 `kill $(cat /tmp/videodb_capture_pid)` 停止进程。PID 文件应在每次运行时覆盖，以便重新运行始终具有正确的 PID。
9. **Start AI pipelines** (in a separate command/script) on each RTStream for audio indexing and visual indexing. Read the RTStream IDs from the saved session info file.
   9. **启动 AI 管道**（在单独的命令/脚本中），在每个 RTStream 上进行音频索引和视觉索引。从保存的会话信息文件中读取 RTStream ID。
10. **Write custom event processing logic** (in a separate command/script) to read real-time events based on your use case. Examples:
    10. **编写自定义事件处理逻辑**（在单独的命令/脚本中），根据您的用例读取实时事件。例如：
    - Log Slack activity when `visual_index` mentions "Slack"
      - 当 `visual_index` 提到 "Slack" 时记录 Slack 活动
    - Summarize discussions when `audio_index` events arrive
      - 当 `audio_index` 事件到达时总结讨论
    - Trigger alerts when specific keywords appear in `transcript`
      - 当 `transcript` 中出现特定关键词时触发警报
    - Track application usage from screen descriptions
      - 从屏幕描述跟踪应用程序使用情况
11. **Stop capture** when done — send SIGTERM to the capture process. It should call `client.stop_capture()` and `client.shutdown()` in its signal handler.
    11. **完成后停止捕获**——向捕获进程发送 SIGTERM。它应在信号处理器中调用 `client.stop_capture()` 和 `client.shutdown()`。
12. **Wait for export** by reading events until you see `capture_session.exported`. This event contains `exported_video_id`, `stream_url`, and `player_url`. This may take several seconds after stopping capture.
    12. **等待导出**，通过读取事件直到看到 `capture_session.exported`。此事件包含 `exported_video_id`、`stream_url` 和 `player_url`。这可能在停止捕获后需要几秒钟。
13. **Stop WebSocket listener** after receiving the export event. Use `kill $(cat /tmp/videodb_ws_pid)` to cleanly terminate it.
    13. **收到导出事件后停止 WebSocket 监听器**。使用 `kill $(cat /tmp/videodb_ws_pid)` 干净地终止它。

---

## Shutdown Sequence

## Shutdown Sequence
## 关闭顺序

Proper shutdown order is important to ensure all events are captured:
正确的关闭顺序很重要，以确保捕获所有事件：

1. **Stop the capture session** — `client.stop_capture()` then `client.shutdown()`
   1. **停止捕获会话**——先 `client.stop_capture()` 然后 `client.shutdown()`
2. **Wait for export event** — poll `/tmp/videodb_events.jsonl` for `capture_session.exported`
   2. **等待导出事件**——轮询 `/tmp/videodb_events.jsonl` 获取 `capture_session.exported`
3. **Stop the WebSocket listener** — `kill $(cat /tmp/videodb_ws_pid)`
   3. **停止 WebSocket 监听器**——`kill $(cat /tmp/videodb_ws_pid)`

Do NOT kill the WebSocket listener before receiving the export event, or you will miss the final video URLs.
在收到导出事件之前不要终止 WebSocket 监听器，否则您将错过最终的 video URL。

---

## Scripts

## Scripts
## 脚本

| Script | Description |
|--------|-------------|
| `scripts/ws_listener.py` | WebSocket event listener (dumps to JSONL) |
| `scripts/ws_listener.py` | WebSocket 事件监听器（转储到 JSONL） |

### ws_listener.py Usage

### ws_listener.py Usage
### ws_listener.py 使用方法

```bash
# Start listener in background (append to existing events)
# 在后台启动监听器（追加到现有事件）
python scripts/ws_listener.py &

# Start listener with clear (new session, clears old events)
# 使用 clear 启动监听器（新会话，清除旧事件）
python scripts/ws_listener.py --clear &

# Custom output directory
# 自定义输出目录
python scripts/ws_listener.py --clear /path/to/events &

# Stop the listener
# 停止监听器
kill $(cat /tmp/videodb_ws_pid)
```

**Options:**
**选项：**

- `--clear`: Clear the events file before starting. Use when starting a new capture session.
  - `--clear`：启动前清除事件文件。在开始新捕获会话时使用。

**Output files:**
**输出文件：**

- `videodb_events.jsonl` - All WebSocket events
  - `videodb_events.jsonl` - 所有 WebSocket 事件
- `videodb_ws_id` - WebSocket connection ID (for `ws_connection_id` parameter)
  - `videodb_ws_id` - WebSocket 连接 ID（用于 `ws_connection_id` 参数）
- `videodb_ws_pid` - Process ID (for stopping the listener)
  - `videodb_ws_pid` - 进程 ID（用于停止监听器）

**Features:**
**特性：**

- Auto-reconnect with exponential backoff on connection drops
  - 连接断开时使用指数退避自动重连
- Graceful shutdown on SIGINT/SIGTERM
  - SIGINT/SIGTERM 时优雅关闭
- PID file for easy process management
  - PID 文件便于进程管理
- Connection status logging
  - 连接状态日志
