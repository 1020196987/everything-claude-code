# RTStream Guide

## RTStream Guide
## RTStream 指南

## Overview

## Overview
## 概述

RTStream enables real-time ingestion of live video streams (RTSP/RTMP) and desktop capture sessions. Once connected, you can record, index, search, and export content from live sources.
RTStream 支持实时摄入直播视频流（RTSP/RTMP）和桌面捕获会话。连接后，您可以从直播源录制、索引、搜索和导出内容。

For code-level details (SDK methods, parameters, examples), see [rtstream-reference.md](rtstream-reference.md).
有关代码级细节（SDK 方法、参数、示例），参见 [rtstream-reference.md](rtstream-reference.md)。

## Use Cases

## Use Cases
## 使用场景

- **Security & Monitoring**: Connect RTSP cameras, detect events, trigger alerts
  - **安全和监控**：连接 RTSP 摄像头，检测事件，触发警报
- **Live Broadcasts**: Ingest RTMP streams, index in real-time, enable instant search
  - **直播**：摄入 RTMP 流，实时索引，即时搜索
- **Meeting Recording**: Capture desktop screen and audio, transcribe live, export recordings
  - **会议录制**：捕获桌面屏幕和音频，实时转录，导出录制
- **Event Processing**: Monitor live feeds, run AI analysis, respond to detected content
  - **事件处理**：监控直播源，运行 AI 分析，响应检测到的内容

## Quick Start

## Quick Start
## 快速开始

1. **Connect to a live stream** (RTSP/RTMP URL) or get RTStream from a capture session
   1. **连接到直播流**（RTSP/RTMP URL）或从捕获会话获取 RTStream
2. **Start ingestion** to begin recording the live content
   2. **开始摄入**以开始录制直播内容
3. **Start AI pipelines** for real-time indexing (audio, visual, transcription)
   3. **启动 AI 管道**进行实时索引（音频、视觉、转录）
4. **Monitor events** via WebSocket for live AI results and alerts
   4. **通过 WebSocket 监控事件**以获取实时 AI 结果和警报
5. **Stop ingestion** when done
   5. **完成后停止摄入**
6. **Export to video** for permanent storage and further processing
   6. **导出为视频**以永久存储和进一步处理
7. **Search the recording** to find specific moments
   7. **搜索录制内容**以找到特定时刻

## RTStream Sources

## RTStream Sources
## RTStream 来源

### From RTSP/RTMP Streams

### From RTSP/RTMP Streams
### 从 RTSP/RTMP 流

Connect directly to a live video source:
直接连接到直播视频源：

```python
rtstream = coll.connect_rtstream(
    url="rtmp://your-stream-server/live/stream-key",
    name="My Live Stream",
)
```

### From Capture Sessions

### From Capture Sessions
### 从捕获会话

Get RTStreams from desktop capture (mic, screen, system audio):
从桌面捕获获取 RTStream（麦克风、屏幕、系统音频）：

```python
session = conn.get_capture_session(session_id)

mics = session.get_rtstream("mic")
displays = session.get_rtstream("screen")
system_audios = session.get_rtstream("system_audio")
```

For capture session workflow, see [capture.md](capture.md).
有关捕获会话工作流，参见 [capture.md](capture.md)。

---

## Scripts

## Scripts
## 脚本

| Script | Description |
|--------|-------------|
| `scripts/ws_listener.py` | WebSocket event listener for real-time AI results |
| `scripts/ws_listener.py` | 用于实时 AI 结果的 WebSocket 事件监听器 |
