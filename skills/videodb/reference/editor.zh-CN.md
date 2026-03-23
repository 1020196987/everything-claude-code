# Timeline Editing Guide

## Timeline Editing Guide
## 时间线编辑指南

VideoDB provides a non-destructive timeline editor for composing videos from multiple assets, adding text and image overlays, mixing audio tracks, and trimming clips — all server-side without re-encoding or local tools. Use this for trimming, combining clips, overlaying audio/music on video, adding subtitles, and layering text or images.
VideoDB 提供非破坏性时间线编辑器，用于从多个资产组合视频、添加文本和图片叠加层、混合音轨和裁剪片段——全部在服务器端完成，无需重新编码或本地工具。用于裁剪、组合片段、在视频上叠加音频/音乐、添加字幕以及叠加文本或图片。

## Prerequisites

## Prerequisites
## 前提条件

Videos, audio, and images **must be uploaded** to a collection before they can be used as timeline assets. For caption overlays, the video must also be **indexed for spoken words**.
视频、音频和图片**必须上传**到 collection 中，才能用作时间线资产。对于字幕叠加，视频还必须**已建立口语索引**。

## Core Concepts

## Core Concepts
## 核心概念

### Timeline

### Timeline
### 时间线

A `Timeline` is a virtual composition layer. Assets are placed on it either **inline** (sequentially on the main track) or as **overlays** (layered at a specific timestamp). Nothing modifies the original media; the final stream is compiled on demand.
`Timeline` 是一个虚拟组合层。资产可以**内联**放置（按顺序在主轨上）或作为**叠加层**（在特定时间戳分层）。原始媒体不会被修改；最终流按需编译。

```python
from videodb.timeline import Timeline

timeline = Timeline(conn)
```

### Assets

### Assets
### 资产

Every element on a timeline is an **asset**. VideoDB provides five asset types:
时间线上的每个元素都是**资产**。VideoDB 提供五种资产类型：

| Asset | Import | Primary Use |
|-------|--------|-------------|
| `VideoAsset` | `from videodb.asset import VideoAsset` | Video clips (trim, sequencing) |
| `VideoAsset` | `from videodb.asset import VideoAsset` | 视频片段（裁剪、排序） |
| `AudioAsset` | `from videodb.asset import AudioAsset` | Music, SFX, narration |
| `AudioAsset` | `from videodb.asset import AudioAsset` | 音乐、音效、配音 |
| `ImageAsset` | `from videodb.asset import ImageAsset` | Logos, thumbnails, overlays |
| `ImageAsset` | `from videodb.asset import ImageAsset` | Logo、缩略图、叠加层 |
| `TextAsset` | `from videodb.asset import TextAsset, TextStyle` | Titles, captions, lower-thirds |
| `TextAsset` | `from videodb.asset import TextAsset, TextStyle` | 标题、说明字幕、下三分之一 |
| `CaptionAsset` | `from videodb.editor import CaptionAsset` | Auto-rendered subtitles (Editor API) |
| `CaptionAsset` | `from videodb.editor import CaptionAsset` | 自动渲染字幕（编辑器 API） |

## Building a Timeline

## Building a Timeline
## 构建时间线

### Add Video Clips Inline

### Add Video Clips Inline
### 内联添加视频片段

Inline assets play one after another on the main video track. The `add_inline` method only accepts `VideoAsset`:
内联资产在主视频轨上按顺序播放。`add_inline` 方法仅接受 `VideoAsset`：

```python
from videodb.asset import VideoAsset

video_a = coll.get_video(video_id_a)
video_b = coll.get_video(video_id_b)

timeline = Timeline(conn)
timeline.add_inline(VideoAsset(asset_id=video_a.id))
timeline.add_inline(VideoAsset(asset_id=video_b.id))

stream_url = timeline.generate_stream()
```

### Trim / Sub-clip

### Trim / Sub-clip
### 裁剪/子片段

Use `start` and `end` on a `VideoAsset` to extract a portion:
使用 `VideoAsset` 上的 `start` 和 `end` 提取一部分：

```python
# Take only seconds 10–30 from the source video
# 仅从源视频中获取 10-30 秒
clip = VideoAsset(asset_id=video.id, start=10, end=30)
timeline.add_inline(clip)
```

### VideoAsset Parameters

### VideoAsset Parameters
### VideoAsset 参数

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `asset_id` | `str` | required | Video media ID |
| `asset_id` | `str` | 必需 | 视频媒体 ID |
| `start` | `float` | `0` | Trim start (seconds) |
| `start` | `float` | `0` | 裁剪开始（秒） |
| `end` | `float\|None` | `None` | Trim end (`None` = full) |
| `end` | `float\|None` | `None` | 裁剪结束（`None` = 完整） |

> **Warning:** The SDK does not validate negative timestamps. Passing `start=-5` is silently accepted but produces broken or unexpected output. Always ensure `start >= 0`, `start < end`, and `end <= video.length` before creating a `VideoAsset`.
> **警告：** SDK 不验证负时间戳。传入 `start=-5` 会被静默接受，但会产生损坏或意外输出。在创建 `VideoAsset` 之前，始终确保 `start >= 0`、`start < end` 且 `end <= video.length`。

## Text Overlays

## Text Overlays
## 文本叠加

Add titles, lower-thirds, or captions at any point on the timeline:
在时间线的任意位置添加标题、下三分之一或说明字幕：

```python
from videodb.asset import TextAsset, TextStyle

title = TextAsset(
    text="Welcome to the Demo",
    duration=5,
    style=TextStyle(
        fontsize=36,
        fontcolor="white",
        boxcolor="black",
        alpha=0.8,
        font="Sans",
    ),
)

# Overlay the title at the very start (t=0)
# 在开头（t=0）叠加标题
timeline.add_overlay(0, title)
```

### TextStyle Parameters

### TextStyle Parameters
### TextStyle 参数

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `fontsize` | `int` | `24` | Font size in pixels |
| `fontsize` | `int` | `24` | 字体大小（像素） |
| `fontcolor` | `str` | `"black"` | CSS colour name or hex |
| `fontcolor` | `str` | `"black"` | CSS 颜色名称或十六进制 |
| `fontcolor_expr` | `str` | `""` | Dynamic font colour expression |
| `fontcolor_expr` | `str` | `""` | 动态字体颜色表达式 |
| `alpha` | `float` | `1.0` | Text opacity (0.0–1.0) |
| `alpha` | `float` | `1.0` | 文字不透明度（0.0-1.0） |
| `font` | `str` | `"Sans"` | Font family |
| `font` | `str` | `"Sans"` | 字体系列 |
| `box` | `bool` | `True` | Enable background box |
| `box` | `bool` | `True` | 启用背景框 |
| `boxcolor` | `str` | `"white"` | Background box colour |
| `boxcolor` | `str` | `"white"` | 背景框颜色 |
| `boxborderw` | `str` | `"10"` | Box border width |
| `boxborderw` | `str` | `"10"` | 框边框宽度 |
| `boxw` | `int` | `0` | Box width override |
| `boxw` | `int` | `0` | 框宽度覆盖 |
| `boxh` | `int` | `0` | Box height override |
| `boxh` | `int` | `0` | 框高度覆盖 |
| `line_spacing` | `int` | `0` | Line spacing |
| `line_spacing` | `int` | `0` | 行间距 |
| `text_align` | `str` | `"T"` | Text alignment within the box |
| `text_align` | `str` | `"T"` | 框内文本对齐 |
| `y_align` | `str` | `"text"` | Vertical alignment reference |
| `y_align` | `str` | `"text"` | 垂直对齐参考 |
| `borderw` | `int` | `0` | Text border width |
| `borderw` | `int` | `0` | 文字边框宽度 |
| `bordercolor` | `str` | `"black"` | Text border colour |
| `bordercolor` | `str` | `"black"` | 文字边框颜色 |
| `expansion` | `str` | `"normal"` | Text expansion mode |
| `expansion` | `str` | `"normal"` | 文字扩展模式 |
| `basetime` | `int` | `0` | Base time for time-based expressions |
| `basetime` | `int` | `0` | 基于时间的表达式的基础时间 |
| `fix_bounds` | `bool` | `False` | Fix text bounds |
| `fix_bounds` | `bool` | `False` | 固定文本边界 |
| `text_shaping` | `bool` | `True` | Enable text shaping |
| `text_shaping` | `bool` | `True` | 启用文字整形 |
| `shadowcolor` | `str` | `"black"` | Shadow colour |
| `shadowcolor` | `str` | `"black"` | 阴影颜色 |
| `shadowx` | `int` | `0` | Shadow X offset |
| `shadowx` | `int` | `0` | 阴影 X 偏移 |
| `shadowy` | `int` | `0` | Shadow Y offset |
| `shadowy` | `int` | `0` | 阴影 Y 偏移 |
| `tabsize` | `int` | `4` | Tab size in spaces |
| `tabsize` | `int` | `4` | 制表符大小（空格） |
| `x` | `str` | `"(main_w-text_w)/2"` | Horizontal position expression |
| `x` | `str` | `"(main_w-text_w)/2"` | 水平位置表达式 |
| `y` | `str` | `"(main_h-text_h)/2"` | Vertical position expression |
| `y` | `str` | `"(main_h-text_h)/2"` | 垂直位置表达式 |

## Audio Overlays

## Audio Overlays
## 音频叠加

Layer background music, sound effects, or voiceover on top of the video track:
在视频轨上叠加背景音乐、音效或配音：

```python
from videodb.asset import AudioAsset

music = coll.get_audio(music_id)

audio_layer = AudioAsset(
    asset_id=music.id,
    disable_other_tracks=False,
    fade_in_duration=2,
    fade_out_duration=2,
)

# Start the music at t=0, overlaid on the video track
# 在 t=0 开始音乐，叠加在视频轨上
timeline.add_overlay(0, audio_layer)
```

### AudioAsset Parameters

### AudioAsset Parameters
### AudioAsset 参数

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `asset_id` | `str` | required | Audio media ID |
| `asset_id` | `str` | 必需 | 音频媒体 ID |
| `start` | `float` | `0` | Trim start (seconds) |
| `start` | `float` | `0` | 裁剪开始（秒） |
| `end` | `float\|None` | `None` | Trim end (`None` = full) |
| `end` | `float\|None` | `None` | 裁剪结束（`None` = 完整） |
| `disable_other_tracks` | `bool` | `True` | When True, mutes other audio tracks |
| `disable_other_tracks` | `bool` | `True` | 为 True 时，静音其他音轨 |
| `fade_in_duration` | `float` | `0` | Fade-in seconds (max 5) |
| `fade_in_duration` | `float` | `0` | 淡入秒数（最大 5） |
| `fade_out_duration` | `float` | `0` | Fade-out seconds (max 5) |
| `fade_out_duration` | `float` | `0` | 淡出秒数（最大 5） |

## Image Overlays

## Image Overlays
## 图片叠加

Add logos, watermarks, or generated images as overlays:
添加 logo、水印或生成的图片作为叠加层：

```python
from videodb.asset import ImageAsset

logo = coll.get_image(logo_id)

logo_overlay = ImageAsset(
    asset_id=logo.id,
    duration=10,
    width=120,
    height=60,
    x=20,
    y=20,
)

timeline.add_overlay(0, logo_overlay)
```

### ImageAsset Parameters

### ImageAsset Parameters
### ImageAsset 参数

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `asset_id` | `str` | required | Image media ID |
| `asset_id` | `str` | 必需 | 图片媒体 ID |
| `width` | `int\|str` | `100` | Display width |
| `width` | `int\|str` | `100` | 显示宽度 |
| `height` | `int\|str` | `100` | Display height |
| `height` | `int\|str` | `100` | 显示高度 |
| `x` | `int` | `80` | Horizontal position (px from left) |
| `x` | `int` | `80` | 水平位置（距离左侧像素） |
| `y` | `int` | `20` | Vertical position (px from top) |
| `y` | `int` | `20` | 垂直位置（距离顶部像素） |
| `duration` | `float\|None` | `None` | Display duration (seconds) |
| `duration` | `float\|None` | `None` | 显示时长（秒） |

## Caption Overlays

## Caption Overlays
## 字幕叠加

There are two ways to add captions to video.
有两种方法可以向视频添加字幕。

### Method 1: Subtitle Workflow (simplest)

### Method 1: Subtitle Workflow (simplest)
### 方法 1：字幕工作流（最简单）

Use `video.add_subtitle()` to burn subtitles directly onto a video stream. This uses the `videodb.timeline.Timeline` internally:
使用 `video.add_subtitle()` 将字幕直接烧录到视频流中。这在内部使用 `videodb.timeline.Timeline`：

```python
from videodb import SubtitleStyle

# Video must have spoken words indexed first (force=True skips if already done)
# 视频必须先建立口语索引（force=True 如果已完成则跳过）
video.index_spoken_words(force=True)

# Add subtitles with default styling
# 使用默认样式添加字幕
stream_url = video.add_subtitle()

# Or customise the subtitle style
# 或自定义字幕样式
stream_url = video.add_subtitle(style=SubtitleStyle(
    font_name="Arial",
    font_size=22,
    primary_colour="&H00FFFFFF",
    bold=True,
))
```

### Method 2: Editor API (advanced)

### Method 2: Editor API (advanced)
### 方法 2：编辑器 API（高级）

The Editor API (`videodb.editor`) provides a track-based composition system with `CaptionAsset`, `Clip`, `Track`, and its own `Timeline`. This is a separate API from the `videodb.timeline.Timeline` used above.
编辑器 API（`videodb.editor`）提供基于轨道的组合系统，包含 `CaptionAsset`、`Clip`、`Track` 和自己的 `Timeline`。这是与上面使用的 `videodb.timeline.Timeline` 分离的 API。

```python
from videodb.editor import (
    CaptionAsset,
    Clip,
    Track,
    Timeline as EditorTimeline,
    FontStyling,
    BorderAndShadow,
    Positioning,
    CaptionAnimation,
)

# Video must have spoken words indexed first (force=True skips if already done)
# 视频必须先建立口语索引（force=True 如果已完成则跳过）
video.index_spoken_words(force=True)

# Create a caption asset
# 创建字幕资产
caption = CaptionAsset(
    src="auto",
    font=FontStyling(name="Clear Sans", size=30),
    primary_color="&H00FFFFFF",
    back_color="&H00000000",
    border=BorderAndShadow(outline=1),
    position=Positioning(margin_v=30),
    animation=CaptionAnimation.box_highlight,
)

# Build an editor timeline with tracks and clips
# 用轨道和片段构建编辑器时间线
editor_tl = EditorTimeline(conn)
track = Track()
track.add_clip(start=0, clip=Clip(asset=caption, duration=video.length))
editor_tl.add_track(track)
stream_url = editor_tl.generate_stream()
```

### CaptionAsset Parameters

### CaptionAsset Parameters
### CaptionAsset 参数

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `src` | `str` | `"auto"` | Caption source (`"auto"` or base64 ASS string) |
| `src` | `str` | `"auto"` | 字幕源（`"auto"` 或 base64 ASS 字符串） |
| `font` | `FontStyling\|None` | `FontStyling()` | Font styling (name, size, bold, italic, etc.) |
| `font` | `FontStyling\|None` | `FontStyling()` | 字体样式（名称、大小、粗体、斜体等） |
| `primary_color` | `str` | `"&H00FFFFFF"` | Primary text colour (ASS format) |
| `primary_color` | `str` | `"&H00FFFFFF"` | 主要文字颜色（ASS 格式） |
| `secondary_color` | `str` | `"&H000000FF"` | Secondary text colour (ASS format) |
| `secondary_color` | `str` | `"&H000000FF"` | 次要文字颜色（ASS 格式） |
| `back_color` | `str` | `"&H00000000"` | Background colour (ASS format) |
| `back_color` | `str` | `"&H00000000"` | 背景颜色（ASS 格式） |
| `border` | `BorderAndShadow\|None` | `BorderAndShadow()` | Border and shadow styling |
| `border` | `BorderAndShadow\|None` | `BorderAndShadow()` | 边框和阴影样式 |
| `position` | `Positioning\|None` | `Positioning()` | Caption alignment and margins |
| `position` | `Positioning\|None` | `Positioning()` | 字幕对齐和边距 |
| `animation` | `CaptionAnimation\|None` | `None` | Animation effect (e.g., `box_highlight`, `reveal`, `karaoke`) |
| `animation` | `CaptionAnimation\|None` | `None` | 动画效果（例如 `box_highlight`、`reveal`、`karaoke`） |

## Compiling & Streaming

## Compiling & Streaming
## 编译和流传输

After assembling a timeline, compile it into a streamable URL. Streams are generated instantly - no render wait times.
组装时间线后，将其编译为可流式传输的 URL。流是即时生成的——无需渲染等待时间。

```python
stream_url = timeline.generate_stream()
print(f"Stream: {stream_url}")
```

For more streaming options (segment streams, search-to-stream, audio playback), see [streaming.md](streaming.md).
有关更多流选项（片段流、搜索到流、音频播放），参见 [streaming.md](streaming.md)。

## Complete Workflow Examples

## Complete Workflow Examples
## 完整工作流示例

### Highlight Reel with Title Card

### Highlight Reel with Title Card
### 带标题卡的精彩集锦

```python
import videodb
from videodb import SearchType
from videodb.exceptions import InvalidRequestError
from videodb.timeline import Timeline
from videodb.asset import VideoAsset, TextAsset, TextStyle

conn = videodb.connect()
coll = conn.get_collection()
video = coll.get_video("your-video-id")

# 1. Search for key moments
# 1. 搜索关键时刻
video.index_spoken_words(force=True)
try:
    results = video.search("product announcement", search_type=SearchType.semantic)
    shots = results.get_shots()
except InvalidRequestError as exc:
    if "No results found" in str(exc):
        shots = []
    else:
        raise

# 2. Build timeline
# 2. 构建时间线
timeline = Timeline(conn)

# Title card
# 标题卡
title = TextAsset(
    text="Product Launch Highlights",
    duration=4,
    style=TextStyle(fontsize=48, fontcolor="white", boxcolor="#1a1a2e", alpha=0.95),
)
timeline.add_overlay(0, title)

# Append each matching clip
# 附加每个匹配的片段
for shot in shots:
    asset = VideoAsset(asset_id=shot.video_id, start=shot.start, end=shot.end)
    timeline.add_inline(asset)

# 3. Generate stream
# 3. 生成流
stream_url = timeline.generate_stream()
print(f"Highlight reel: {stream_url}")
```

### Logo Overlay with Background Music

### Logo Overlay with Background Music
### 带背景音乐的 Logo 叠加

```python
import videodb
from videodb.timeline import Timeline
from videodb.asset import VideoAsset, AudioAsset, ImageAsset

conn = videodb.connect()
coll = conn.get_collection()

main_video = coll.get_video(main_video_id)
music = coll.get_audio(music_id)
logo = coll.get_image(logo_id)

timeline = Timeline(conn)

# Main video track
# 主视频轨
timeline.add_inline(VideoAsset(asset_id=main_video.id))

# Background music — disable_other_tracks=False to mix with video audio
# 背景音乐 — disable_other_tracks=False 与视频音频混合
timeline.add_overlay(
    0,
    AudioAsset(asset_id=music.id, disable_other_tracks=False, fade_in_duration=3),
)

# Logo in top-right corner for first 10 seconds
# 前 10 秒右上角的 Logo
timeline.add_overlay(
    0,
    ImageAsset(asset_id=logo.id, duration=10, x=1140, y=20, width=120, height=60),
)

stream_url = timeline.generate_stream()
print(f"Final video: {stream_url}")
```

### Multi-Clip Montage from Multiple Videos

### Multi-Clip Montage from Multiple Videos
### 来自多个视频的多片段蒙太奇

```python
import videodb
from videodb.timeline import Timeline
from videodb.asset import VideoAsset, TextAsset, TextStyle

conn = videodb.connect()
coll = conn.get_collection()

clips = [
    {"video_id": "vid_001", "start": 5, "end": 15, "label": "Scene 1"},
    {"video_id": "vid_002", "start": 0, "end": 20, "label": "Scene 2"},
    {"video_id": "vid_003", "start": 30, "end": 45, "label": "Scene 3"},
]

timeline = Timeline(conn)
timeline_offset = 0.0

for clip in clips:
    # Add a label as an overlay on each clip
    # 在每个片段上添加标签作为叠加层
    label = TextAsset(
        text=clip["label"],
        duration=2,
        style=TextStyle(fontsize=32, fontcolor="white", boxcolor="#333333"),
    )
    timeline.add_inline(
        VideoAsset(asset_id=clip["video_id"], start=clip["start"], end=clip["end"])
    )
    timeline.add_overlay(timeline_offset, label)
    timeline_offset += clip["end"] - clip["start"]

stream_url = timeline.generate_stream()
print(f"Montage: {stream_url}")
```

## Two Timeline APIs

## Two Timeline APIs
## 两个时间线 API

VideoDB has two separate timeline systems. They are **not interchangeable**:
VideoDB 有两个独立的时间线系统。它们**不可互换**：

| | `videodb.timeline.Timeline` | `videodb.editor.Timeline` (Editor API) |
|---|---|---|
| **Import** | `from videodb.timeline import Timeline` | `from videodb.editor import Timeline as EditorTimeline` |
| **Assets** | `VideoAsset`, `AudioAsset`, `ImageAsset`, `TextAsset` | `CaptionAsset`, `Clip`, `Track` |
| **Methods** | `add_inline()`, `add_overlay()` | `add_track()` with `Track` / `Clip` |
| **Best for** | Video composition, overlays, multi-clip editing | Caption/subtitle styling with animations |
| | | |
| **导入** | `from videodb.timeline import Timeline` | `from videodb.editor import Timeline as EditorTimeline` |
| **资产** | `VideoAsset`、`AudioAsset`、`ImageAsset`、`TextAsset` | `CaptionAsset`、`Clip`、`Track` |
| **方法** | `add_inline()`、`add_overlay()` | `add_track()` 配合 `Track`/`Clip` |
| **最适合** | 视频组合、叠加、多片段编辑 | 带动画的字幕样式 |

Do not mix assets from one API into the other. `CaptionAsset` only works with the Editor API. `VideoAsset` / `AudioAsset` / `ImageAsset` / `TextAsset` only work with `videodb.timeline.Timeline`.
不要将一个 API 的资产混合到另一个 API 中。`CaptionAsset` 仅适用于编辑器 API。`VideoAsset`/`AudioAsset`/`ImageAsset`/`TextAsset` 仅适用于 `videodb.timeline.Timeline`。

## Limitations & Constraints

## Limitations & Constraints
## 限制和约束

The timeline editor is designed for **non-destructive linear composition**. The following operations are **not supported**:
时间线编辑器设计用于**非破坏性线性组合**。以下操作**不支持**：

### Not Possible

### Not Possible
### 不可能实现

| Limitation | Detail |
|---|---|
| **No transitions or effects** | No crossfades, wipes, dissolves, or transitions between clips. All cuts are hard cuts. |
| **无转场或效果** | 无交叉淡入淡出、擦拭、叠化或片段之间的转场。所有剪辑都是硬切。 |
| **No video-on-video (picture-in-picture)** | `add_inline()` only accepts `VideoAsset`. You cannot overlay one video stream on top of another. Image overlays can approximate static PiP but not live video. |
| **无视频叠加（画中画）** | `add_inline()` 仅接受 `VideoAsset`。您不能将一个视频流叠加在另一个之上。图片叠加可以近似静态 PiP，但不能用于实时视频。 |
| **No speed or playback control** | No slow-motion, fast-forward, reverse playback, or time remapping. `VideoAsset` has no `speed` parameter. |
| **无速度或播放控制** | 无慢动作、快进、倒放或时间重映射。`VideoAsset` 没有 `speed` 参数。 |
| **No crop, zoom, or pan** | Cannot crop a region of a video frame, apply zoom effects, or pan across a frame. `video.reframe()` is for aspect-ratio conversion only. |
| **无裁剪、缩放或平移** | 无法裁剪视频帧的区域、应用缩放效果或平移画面。`video.reframe()` 仅用于宽高比转换。 |
| **No video filters or color grading** | No brightness, contrast, saturation, hue, or color correction adjustments. |
| **无视频滤镜或调色** | 无亮度、对比度、饱和度、色相或色彩校正调整。 |
| **No animated text** | `TextAsset` is static for its full duration. No fade-in/out, movement, or animation. For animated captions, use `CaptionAsset` with the Editor API. |
| **无动态文字** | `TextAsset` 在整个时长内是静态的。无淡入/淡出、移动或动画。对于动态字幕，使用编辑器 API 的 `CaptionAsset`。 |
| **No mixed text styling** | A single `TextAsset` has one `TextStyle`. Cannot mix bold, italic, or colors within a single text block. |
| **无混合文字样式** | 单个 `TextAsset` 只有一个 `TextStyle`。不能在单个文本块中混合粗体、斜体或颜色。 |
| **No blank or solid-color clips** | Cannot create a solid color frame, black screen, or standalone title card. Text and image overlays require a `VideoAsset` beneath them on the inline track. |
| **无空白或纯色片段** | 无法创建纯色帧、黑屏或独立标题卡。文本和图片叠加需要在其下方的主轨上有 `VideoAsset`。 |
| **No audio volume control** | `AudioAsset` has no `volume` parameter. Audio is either full volume or muted via `disable_other_tracks`. Cannot mix at a reduced level. |
| **无音频音量控制** | `AudioAsset` 没有 `volume` 参数。音频要么全音量，要么通过 `disable_other_tracks` 静音。无法以降低的音量混合。 |
| **No keyframe animation** | Cannot change overlay properties over time (e.g., move an image from position A to B). |
| **无关键帧动画** | 无法随时间改变叠加属性（例如，将图片从位置 A 移动到 B）。 |

### Constraints

### Constraints
### 约束

| Constraint | Detail |
|---|---|
| **Audio fade max 5 seconds** | `fade_in_duration` and `fade_out_duration` are capped at 5 seconds each. |
| **音频淡入淡出最大 5 秒** | `fade_in_duration` 和 `fade_out_duration` 各自上限为 5 秒。 |
| **Overlay positioning is absolute** | Overlays use absolute timestamps from the timeline start. Rearranging inline clips does not move their overlays. |
| **叠加层定位是绝对的** | 叠加层使用从时间线开始的时间戳。重新排列内联片段不会移动其叠加层。 |
| **Inline track is video only** | `add_inline()` only accepts `VideoAsset`. Audio, image, and text must use `add_overlay()`. |
| **主轨仅限视频** | `add_inline()` 仅接受 `VideoAsset`。音频、图片和文本必须使用 `add_overlay()`。 |
| **No overlay-to-clip binding** | Overlays are placed at a fixed timeline timestamp. There is no way to attach an overlay to a specific inline clip so it moves with it. |
| **无叠加层到片段的绑定** | 叠加层放置在固定的时间线时间戳处。无法将叠加层附加到特定的内联片段上使其跟随移动。 |

## Tips

## Tips
## 提示

- **Non-destructive**: Timelines never modify source media. You can create multiple timelines from the same assets.
  - **非破坏性**：时间线永远不会修改源媒体。您可以从相同的资产创建多个时间线。
- **Overlay stacking**: Multiple overlays can start at the same timestamp. Audio overlays mix together; image/text overlays layer in add-order.
  - **叠加层堆叠**：多个叠加层可以在同一时间戳开始。音频叠加层混合在一起；图片/文本叠加层按添加顺序分层。
- **Inline is VideoAsset only**: `add_inline()` only accepts `VideoAsset`. Use `add_overlay()` for `AudioAsset`, `ImageAsset`, and `TextAsset`.
  - **内联仅限 VideoAsset**：`add_inline()` 仅接受 `VideoAsset`。对 `AudioAsset`、`ImageAsset` 和 `TextAsset` 使用 `add_overlay()`。
- **Trim precision**: `start`/`end` on `VideoAsset` and `AudioAsset` are in seconds.
  - **裁剪精度**：`VideoAsset` 和 `AudioAsset` 上的 `start`/`end` 以秒为单位。
- **Muting video audio**: Set `disable_other_tracks=True` on `AudioAsset` to mute the original video audio when overlaying music or narration.
  - **静音视频音频**：在 `AudioAsset` 上设置 `disable_other_tracks=True` 以在叠加音乐或配音时静音原始视频音频。
- **Fade limits**: `fade_in_duration` and `fade_out_duration` on `AudioAsset` have a maximum of 5 seconds.
  - **淡入淡出限制**：`AudioAsset` 上的 `fade_in_duration` 和 `fade_out_duration` 最大为 5 秒。
- **Generated media**: Use `coll.generate_music()`, `coll.generate_sound_effect()`, `coll.generate_voice()`, and `coll.generate_image()` to create media that can be used as timeline assets immediately.
  - **生成媒体**：使用 `coll.generate_music()`、`coll.generate_sound_effect()`、`coll.generate_voice()` 和 `coll.generate_image()` 创建可立即用作时间线资产的媒体。
