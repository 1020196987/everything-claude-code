# Use Cases

## Use Cases
## 使用场景

Common workflows and what VideoDB enables. For code details, see [api-reference.md](api-reference.md), [capture.md](capture.md), [editor.md](editor.md), and [search.md](search.md).
常见工作流和 VideoDB 实现的场景。代码详情，参见 [api-reference.md](api-reference.md)、[capture.md](capture.md)、[editor.md](editor.md) 和 [search.md](search.md)。

---

## Video Search & Highlights

## Video Search & Highlights
## 视频搜索和精彩集锦

### Create Highlight Reels

### Create Highlight Reels
### 创建精彩集锦

Upload a long video (conference talk, lecture, meeting recording), search for key moments by topic ("product announcement", "Q&A session", "demo"), and automatically compile matching segments into a shareable highlight reel.
上传长视频（会议演讲、讲座、会议录制），按主题搜索关键时刻（"产品发布"、"问答环节"、"演示"），并将匹配的片段自动编译成分享型精彩集锦。

### Build Searchable Video Libraries

### Build Searchable Video Libraries
### 构建可搜索视频库

Batch upload videos to a collection, index them for spoken word search, then query across the entire library. Find specific topics across hundreds of hours of content instantly.
批量上传视频到 collection，为其建立口语搜索索引，然后在整个库中查询。在数百小时的内容中即时找到特定主题。

### Extract Specific Clips

### Extract Specific Clips
### 提取特定片段

Search for moments matching a query ("budget discussion", "action items") and extract each matching segment as an individual clip with its own stream URL.
搜索匹配查询的时刻（"预算讨论"、"行动项目"），并将每个匹配的片段提取为具有自己流 URL 的单独片段。

---

## Video Enhancement

## Video Enhancement
## 视频增强

### Add Professional Polish

### Add Professional Polish
### 添加专业润色

Take raw footage and enhance it with:
使用以下方式增强原始素材：

- Auto-generated subtitles from speech
  - 从语音自动生成字幕
- Custom thumbnails at specific timestamps
  - 在特定时间戳的自定义缩略图
- Background music overlays
  - 背景音乐叠加
- Intro/outro sequences with generated images
  - 使用生成的图片制作片头/片尾序列

### AI-Enhanced Content

### AI-Enhanced Content
### AI 增强内容

Combine existing video with generative AI:
将现有视频与生成式 AI 结合：

- Generate text summaries from transcript
  - 从转录文本生成摘要
- Create background music matching video duration
  - 创建与视频时长匹配的背景音乐
- Generate title cards and overlay images
  - 生成标题卡和叠加图片
- Mix all elements into a polished final output
  - 将所有元素混合成精美的最终输出

---

## Real-Time Capture (Desktop/Meeting)

## Real-Time Capture (Desktop/Meeting)
## 实时捕获（桌面/会议）

### Screen + Audio Recording with AI

### Screen + Audio Recording with AI
### 带 AI 的屏幕 + 音频录制

Capture screen, microphone, and system audio simultaneously. Get real-time:
同时捕获屏幕、麦克风和系统音频。获取实时：

- **Live transcription** - Speech to text as it happens
  - **实时转录** - 语音即时转文字
- **Audio summaries** - Periodic AI-generated summaries of discussions
  - **音频摘要** - 周期性的 AI 生成讨论摘要
- **Visual indexing** - AI descriptions of screen activity
  - **视觉索引** - AI 对屏幕活动的描述

### Meeting Capture with Summarization

### Meeting Capture with Summarization
### 带摘要的会议捕获

Record meetings with live transcription of all participants. Get periodic summaries with key discussion points, decisions, and action items delivered in real-time.
录制会议的参与者实时转录。实时获取包含关键讨论点、决策和行动项目的周期性摘要。

### Screen Activity Tracking

### Screen Activity Tracking
### 屏幕活动跟踪

Track what's happening on screen with AI-generated descriptions:
使用 AI 生成的描述跟踪屏幕上的活动：

- "User is browsing a spreadsheet in Google Sheets"
  - "用户在 Google Sheets 中浏览电子表格"
- "User switched to a code editor with a Python file"
  - "用户切换到带有 Python 文件的代码编辑器"
- "Video call with screen sharing enabled"
  - "启用了屏幕共享的视频通话"

### Post-Session Processing

### Post-Session Processing
### 会话后处理

After capture ends, the recording is exported as a permanent video. Then:
捕获结束后，录制导出为永久视频。然后：

- Generate searchable transcript
  - 生成可搜索的转录文本
- Search for specific topics within the recording
  - 在录制中搜索特定主题
- Extract clips of important moments
  - 提取重要时刻的片段
- Share via stream URL or player link
  - 通过流 URL 或播放器链接分享

---

## Live Stream Intelligence (RTSP/RTMP)

## Live Stream Intelligence (RTSP/RTMP)
## 直播流智能（RTSP/RTMP）

### Connect External Streams

### Connect External Streams
### 连接外部流

Ingest live video from RTSP/RTMP sources (security cameras, encoders, broadcasts). Process and index content in real-time.
从 RTSP/RTMP 源（安全摄像头、编码器、广播）摄入直播视频。实时处理和索引内容。

### Real-Time Event Detection

### Real-Time Event Detection
### 实时事件检测

Define events to detect in live streams:
定义要检测的直播流事件：

- "Person entering restricted area"
  - "人员进入限制区域"
- "Traffic violation at intersection"
  - "交叉路口的交通违规"
- "Product visible on shelf"
  - "货架上可见的产品"

Get alerts via WebSocket or webhook when events occur.
事件发生时通过 WebSocket 或 webhook 获取警报。

### Live Stream Search

### Live Stream Search
### 直播流搜索

Search across recorded live stream content. Find specific moments and generate clips from hours of continuous footage.
在录制的直播流内容中搜索。从数小时的连续镜头中找到特定时刻并生成片段。

---

## Content Moderation & Safety

## Content Moderation & Safety
## 内容审核和安全

### Automated Content Review

### Automated Content Review
### 自动内容审核

Index video scenes with AI and search for problematic content. Flag videos containing violence, inappropriate content, or policy violations.
使用 AI 索引视频场景并搜索问题内容。标记包含暴力、不当内容或政策违规的视频。

### Profanity Detection

### Profanity Detection
### 脏话检测

Detect and locate profanity in audio. Optionally overlay beep sounds at detected timestamps.
检测并定位音频中的脏话。可选地在检测到的时间戳叠加哔声。

---

## Platform Integration

## Platform Integration
## 平台集成

### Social Media Formatting

### Social Media Formatting
### 社交媒体格式化

Reframe videos for different platforms:
为不同平台重新构图视频：

- Vertical (9:16) for TikTok, Reels, Shorts
  - 竖屏（9:16）用于 TikTok、Reels、Shorts
- Square (1:1) for Instagram feed
  - 方形（1:1）用于 Instagram 动态
- Landscape (16:9) for YouTube
  - 横屏（16:9）用于 YouTube

### Transcode for Delivery

### Transcode for Delivery
### 转码以供分发

Change resolution, bitrate, or quality for different delivery targets. Output optimized streams for web, mobile, or broadcast.
为不同的分发目标更改分辨率、比特率或质量。输出针对 web、移动端或广播优化的流。

### Generate Shareable Links

### Generate Shareable Links
### 生成分享链接

Every operation produces playable stream URLs. Embed in web players, share directly, or integrate with existing platforms.
每个操作都会生成可播放的流 URL。嵌入 Web 播放器、直接分享或与现有平台集成。

---

## Workflow Summary

## Workflow Summary
## 工作流摘要

| Goal | VideoDB Approach |
|------|------------------|
| Find moments in video | Index spoken words/scenes → Search → Compile clips |
| 创建精彩集锦 | 索引口语/场景 → 搜索 → 编译片段 |
| Create highlights | Search multiple topics → Build timeline → Generate stream |
| 创建精彩集锦 | 搜索多个主题 → 构建时间线 → 生成流 |
| Add subtitles | Index spoken words → Add subtitle overlay |
| 添加字幕 | 索引口语 → 添加字幕叠加 |
| Record screen + AI | Start capture → Run AI pipelines → Export video |
| 录制屏幕 + AI | 开始捕获 → 运行 AI 管道 → 导出视频 |
| Monitor live streams | Connect RTSP → Index scenes → Create alerts |
| 监控直播流 | 连接 RTSP → 索引场景 → 创建警报 |
| Reformat for social | Reframe to target aspect ratio |
| 为社交媒体重新格式化 | 重新构图为目标宽高比 |
| Combine clips | Build timeline with multiple assets → Generate stream |
| 组合片段 | 用多个资产构建时间线 → 生成流 |
