# Complete API Reference

## Complete API Reference
## 完整 API 参考

Reference material for the VideoDB skill. For usage guidance and workflow selection, start with [../SKILL.md](../SKILL.md).
VideoDB 技能参考资料。使用指导和流程选择，请从 [../SKILL.md](../SKILL.md) 开始。

## Connection

## Connection
## 连接

```python
import videodb

conn = videodb.connect(
    api_key="your-api-key",      # or set VIDEO_DB_API_KEY env var
    base_url=None,                # custom API endpoint (optional)
)
```

**Returns:** `Connection` object
**返回：** `Connection` 对象

### Connection Methods

### Connection Methods
### 连接方法

| Method | Returns | Description |
|--------|---------|-------------|
| `conn.get_collection(collection_id="default")` | `Collection` | Get collection (default if no ID) |
| `conn.get_collection(collection_id="default")` | `Collection` | 获取 collection（无 ID 时为默认） |
| `conn.get_collections()` | `list[Collection]` | List all collections |
| `conn.get_collections()` | `list[Collection]` | 列出所有 collection |
| `conn.create_collection(name, description, is_public=False)` | `Collection` | Create new collection |
| `conn.create_collection(name, description, is_public=False)` | `Collection` | 创建新的 collection |
| `conn.update_collection(id, name, description)` | `Collection` | Update a collection |
| `conn.update_collection(id, name, description)` | `Collection` | 更新 collection |
| `conn.check_usage()` | `dict` | Get account usage stats |
| `conn.check_usage()` | `dict` | 获取账户使用统计 |
| `conn.upload(source, media_type, name, ...)` | `Video\|Audio\|Image` | Upload to default collection |
| `conn.upload(source, media_type, name, ...)` | `Video\|Audio\|Image` | 上传到默认 collection |
| `conn.record_meeting(meeting_url, bot_name, ...)` | `Meeting` | Record a meeting |
| `conn.record_meeting(meeting_url, bot_name, ...)` | `Meeting` | 录制会议 |
| `conn.create_capture_session(...)` | `CaptureSession` | Create a capture session (see [capture-reference.md](capture-reference.md)) |
| `conn.create_capture_session(...)` | `CaptureSession` | 创建捕获会话（参见 [capture-reference.md](capture-reference.md)） |
| `conn.youtube_search(query, result_threshold, duration)` | `list[dict]` | Search YouTube |
| `conn.youtube_search(query, result_threshold, duration)` | `list[dict]` | 搜索 YouTube |
| `conn.transcode(source, callback_url, mode, ...)` | `str` | Transcode video (returns job ID) |
| `conn.transcode(source, callback_url, mode, ...)` | `str` | 转码视频（返回 job ID） |
| `conn.get_transcode_details(job_id)` | `dict` | Get transcode job status and details |
| `conn.get_transcode_details(job_id)` | `dict` | 获取转码 job 状态和详情 |
| `conn.connect_websocket(collection_id)` | `WebSocketConnection` | Connect to WebSocket (see [capture-reference.md](capture-reference.md)) |
| `conn.connect_websocket(collection_id)` | `WebSocketConnection` | 连接到 WebSocket（参见 [capture-reference.md](capture-reference.md)） |

### Transcode

### Transcode
### 转码

Transcode a video from a URL with custom resolution, quality, and audio settings. Processing happens server-side — no local ffmpeg required.
从 URL 转码视频，支持自定义分辨率、质量和音频设置。处理在服务器端完成——无需本地 ffmpeg。

```python
from videodb import TranscodeMode, VideoConfig, AudioConfig

job_id = conn.transcode(
    source="https://example.com/video.mp4",
    callback_url="https://example.com/webhook",
    mode=TranscodeMode.economy,
    video_config=VideoConfig(resolution=720, quality=23),
    audio_config=AudioConfig(mute=False),
)
```

#### transcode Parameters

#### transcode Parameters
#### 转码参数

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `source` | `str` | required | URL of the video to transcode (preferably a downloadable URL) |
| `source` | `str` | 必需 | 要转码的视频 URL（最好是可下载的 URL） |
| `callback_url` | `str` | required | URL to receive the callback when transcoding completes |
| `callback_url` | `str` | 必需 | 转码完成时接收回调的 URL |
| `mode` | `TranscodeMode` | `TranscodeMode.economy` | Transcoding speed: `economy` or `lightning` |
| `mode` | `TranscodeMode` | `TranscodeMode.economy` | 转码速度：`economy` 或 `lightning` |
| `video_config` | `VideoConfig` | `VideoConfig()` | Video encoding settings |
| `video_config` | `VideoConfig` | `VideoConfig()` | 视频编码设置 |
| `audio_config` | `AudioConfig` | `AudioConfig()` | Audio encoding settings |
| `audio_config` | `AudioConfig` | `AudioConfig()` | 音频编码设置 |

Returns a job ID (`str`). Use `conn.get_transcode_details(job_id)` to check job status.
返回一个 job ID（`str`）。使用 `conn.get_transcode_details(job_id)` 检查 job 状态。

```python
details = conn.get_transcode_details(job_id)
```

#### VideoConfig

#### VideoConfig
#### VideoConfig

```python
from videodb import VideoConfig, ResizeMode

config = VideoConfig(
    resolution=720,              # Target resolution height (e.g. 480, 720, 1080)
    quality=23,                  # Encoding quality (lower = better, default 23)
    framerate=30,                # Target framerate
    aspect_ratio="16:9",         # Target aspect ratio
    resize_mode=ResizeMode.crop, # How to fit: crop, fit, or pad
)
```

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `resolution` | `int\|None` | `None` | Target resolution height in pixels |
| `resolution` | `int\|None` | `None` | 目标分辨率高度（像素） |
| `quality` | `int` | `23` | Encoding quality (lower = higher quality) |
| `quality` | `int` | `23` | 编码质量（越低 = 质量越高） |
| `framerate` | `int\|None` | `None` | Target framerate |
| `framerate` | `int\|None` | `None` | 目标帧率 |
| `aspect_ratio` | `str\|None` | `None` | Target aspect ratio (e.g. `"16:9"`, `"9:16"`) |
| `aspect_ratio` | `str\|None` | `None` | 目标宽高比（例如 `"16:9"`、`"9:16"`） |
| `resize_mode` | `str` | `ResizeMode.crop` | Resize strategy: `crop`, `fit`, or `pad` |
| `resize_mode` | `str` | `ResizeMode.crop` | 调整策略：`crop`、`fit` 或 `pad` |

#### AudioConfig

#### AudioConfig
#### AudioConfig

```python
from videodb import AudioConfig

config = AudioConfig(mute=False)
```

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `mute` | `bool` | `False` | Mute the audio track |
| `mute` | `bool` | `False` | 静音音轨 |

## Collections

## Collections
## Collections

```python
coll = conn.get_collection()
```

### Collection Methods

### Collection Methods
### Collection 方法

| Method | Returns | Description |
|--------|---------|-------------|
| `coll.get_videos()` | `list[Video]` | List all videos |
| `coll.get_videos()` | `list[Video]` | 列出所有视频 |
| `coll.get_video(video_id)` | `Video` | Get specific video |
| `coll.get_video(video_id)` | `Video` | 获取特定视频 |
| `coll.get_audios()` | `list[Audio]` | List all audios |
| `coll.get_audios()` | `list[Audio]` | 列出所有音频 |
| `coll.get_audio(audio_id)` | `Audio` | Get specific audio |
| `coll.get_audio(audio_id)` | `Audio` | 获取特定音频 |
| `coll.get_images()` | `list[Image]` | List all images |
| `coll.get_images()` | `list[Image]` | 列出所有图片 |
| `coll.get_image(image_id)` | `Image` | Get specific image |
| `coll.get_image(image_id)` | `Image` | 获取特定图片 |
| `coll.upload(url=None, file_path=None, media_type=None, name=None)` | `Video\|Audio\|Image` | Upload media |
| `coll.upload(url=None, file_path=None, media_type=None, name=None)` | `Video\|Audio\|Image` | 上传媒体 |
| `coll.search(query, search_type, index_type, score_threshold, namespace, scene_index_id, ...)` | `SearchResult` | Search across collection (semantic only; keyword and scene search raise `NotImplementedError`) |
| `coll.search(query, search_type, index_type, score_threshold, namespace, scene_index_id, ...)` | `SearchResult` | 在 collection 中搜索（仅语义搜索；关键词和场景搜索会引发 `NotImplementedError`） |
| `coll.generate_image(prompt, aspect_ratio="1:1")` | `Image` | Generate image with AI |
| `coll.generate_image(prompt, aspect_ratio="1:1")` | `Image` | 使用 AI 生成图片 |
| `coll.generate_video(prompt, duration=5)` | `Video` | Generate video with AI |
| `coll.generate_video(prompt, duration=5)` | `Video` | 使用 AI 生成视频 |
| `coll.generate_music(prompt, duration=5)` | `Audio` | Generate music with AI |
| `coll.generate_music(prompt, duration=5)` | `Audio` | 使用 AI 生成音乐 |
| `coll.generate_sound_effect(prompt, duration=2)` | `Audio` | Generate sound effect |
| `coll.generate_sound_effect(prompt, duration=2)` | `Audio` | 生成音效 |
| `coll.generate_voice(text, voice_name="Default")` | `Audio` | Generate speech from text |
| `coll.generate_voice(text, voice_name="Default")` | `Audio` | 从文本生成语音 |
| `coll.generate_text(prompt, model_name="basic", response_type="text")` | `dict` | LLM text generation — access result via `["output"]` |
| `coll.generate_text(prompt, model_name="basic", response_type="text")` | `dict` | LLM 文本生成——通过 `["output"]` 访问结果 |
| `coll.dub_video(video_id, language_code)` | `Video` | Dub video into another language |
| `coll.dub_video(video_id, language_code)` | `Video` | 为视频配音为另一种语言 |
| `coll.record_meeting(meeting_url, bot_name, ...)` | `Meeting` | Record a live meeting |
| `coll.record_meeting(meeting_url, bot_name, ...)` | `Meeting` | 录制直播会议 |
| `coll.create_capture_session(...)` | `CaptureSession` | Create a capture session (see [capture-reference.md](capture-reference.md)) |
| `coll.create_capture_session(...)` | `CaptureSession` | 创建捕获会话（参见 [capture-reference.md](capture-reference.md)） |
| `coll.get_capture_session(...)` | `CaptureSession` | Retrieve capture session (see [capture-reference.md](capture-reference.md)) |
| `coll.get_capture_session(...)` | `CaptureSession` | 获取捕获会话（参见 [capture-reference.md](capture-reference.md)） |
| `coll.connect_rtstream(url, name, ...)` | `RTStream` | Connect to a live stream (see [rtstream-reference.md](rtstream-reference.md)) |
| `coll.connect_rtstream(url, name, ...)` | `RTStream` | 连接到直播流（参见 [rtstream-reference.md](rtstream-reference.md)） |
| `coll.make_public()` | `None` | Make collection public |
| `coll.make_public()` | `None` | 公开 collection |
| `coll.make_private()` | `None` | Make collection private |
| `coll.make_private()` | `None` | 私有化 collection |
| `coll.delete_video(video_id)` | `None` | Delete a video |
| `coll.delete_video(video_id)` | `None` | 删除视频 |
| `coll.delete_audio(audio_id)` | `None` | Delete an audio |
| `coll.delete_audio(audio_id)` | `None` | 删除音频 |
| `coll.delete_image(image_id)` | `None` | Delete an image |
| `coll.delete_image(image_id)` | `None` | 删除图片 |
| `coll.delete()` | `None` | Delete the collection |
| `coll.delete()` | `None` | 删除 collection |

### Upload Parameters

### Upload Parameters
### 上传参数

```python
video = coll.upload(
    url=None,            # Remote URL (HTTP, YouTube)
    file_path=None,      # Local file path
    media_type=None,     # "video", "audio", or "image" (auto-detected if omitted)
    name=None,           # Custom name for the media
    description=None,    # Description
    callback_url=None,   # Webhook URL for async notification
)
```

## Video Object

## Video Object
## 视频对象

```python
video = coll.get_video(video_id)
```

### Video Properties

### Video Properties
### 视频属性

| Property | Type | Description |
|----------|------|-------------|
| `video.id` | `str` | Unique video ID |
| `video.id` | `str` | 唯一视频 ID |
| `video.collection_id` | `str` | Parent collection ID |
| `video.collection_id` | `str` | 父 collection ID |
| `video.name` | `str` | Video name |
| `video.name` | `str` | 视频名称 |
| `video.description` | `str` | Video description |
| `video.description` | `str` | 视频描述 |
| `video.length` | `float` | Duration in seconds |
| `video.length` | `float` | 时长（秒） |
| `video.stream_url` | `str` | Default stream URL |
| `video.stream_url` | `str` | 默认流 URL |
| `video.player_url` | `str` | Player embed URL |
| `video.player_url` | `str` | 播放器嵌入 URL |
| `video.thumbnail_url` | `str` | Thumbnail URL |
| `video.thumbnail_url` | `str` | 缩略图 URL |

### Video Methods

### Video Methods
### 视频方法

| Method | Returns | Description |
|--------|---------|-------------|
| `video.generate_stream(timeline=None)` | `str` | Generate stream URL (optional timeline of `[(start, end)]` tuples) |
| `video.generate_stream(timeline=None)` | `str` | 生成流 URL（可选的 `[(start, end)]` 元组时间线） |
| `video.play()` | `str` | Open stream in browser, returns player URL |
| `video.play()` | `str` | 在浏览器中打开流，返回播放器 URL |
| `video.index_spoken_words(language_code=None, force=False)` | `None` | Index speech for search. Use `force=True` to skip if already indexed. |
| `video.index_spoken_words(language_code=None, force=False)` | `None` | 为搜索建立语音索引。使用 `force=True` 跳过已索引的内容。 |
| `video.index_scenes(extraction_type, prompt, extraction_config, metadata, model_name, name, scenes, callback_url)` | `str` | Index visual scenes (returns scene_index_id) |
| `video.index_scenes(extraction_type, prompt, extraction_config, metadata, model_name, name, scenes, callback_url)` | `str` | 为视觉场景建立索引（返回 scene_index_id） |
| `video.index_visuals(prompt, batch_config, ...)` | `str` | Index visuals (returns scene_index_id) |
| `video.index_visuals(prompt, batch_config, ...)` | `str` | 为视觉内容建立索引（返回 scene_index_id） |
| `video.index_audio(prompt, model_name, ...)` | `str` | Index audio with LLM (returns scene_index_id) |
| `video.index_audio(prompt, model_name, ...)` | `str` | 使用 LLM 为音频建立索引（返回 scene_index_id） |
| `video.get_transcript(start=None, end=None)` | `list[dict]` | Get timestamped transcript |
| `video.get_transcript(start=None, end=None)` | `list[dict]` | 获取带时间戳的转录文本 |
| `video.get_transcript_text(start=None, end=None)` | `str` | Get full transcript text |
| `video.get_transcript_text(start=None, end=None)` | `str` | 获取完整转录文本 |
| `video.generate_transcript(force=None)` | `dict` | Generate transcript |
| `video.generate_transcript(force=None)` | `dict` | 生成转录文本 |
| `video.translate_transcript(language, additional_notes)` | `list[dict]` | Translate transcript |
| `video.translate_transcript(language, additional_notes)` | `list[dict]` | 翻译转录文本 |
| `video.search(query, search_type, index_type, filter, **kwargs)` | `SearchResult` | Search within video |
| `video.search(query, search_type, index_type, filter, **kwargs)` | `SearchResult` | 在视频内搜索 |
| `video.add_subtitle(style=SubtitleStyle())` | `str` | Add subtitles (returns stream URL) |
| `video.add_subtitle(style=SubtitleStyle())` | `str` | 添加字幕（返回流 URL） |
| `video.generate_thumbnail(time=None)` | `str\|Image` | Generate thumbnail |
| `video.generate_thumbnail(time=None)` | `str\|Image` | 生成缩略图 |
| `video.get_thumbnails()` | `list[Image]` | Get all thumbnails |
| `video.get_thumbnails()` | `list[Image]` | 获取所有缩略图 |
| `video.extract_scenes(extraction_type, extraction_config)` | `SceneCollection` | Extract scenes |
| `video.extract_scenes(extraction_type, extraction_config)` | `SceneCollection` | 提取场景 |
| `video.reframe(start, end, target, mode, callback_url)` | `Video\|None` | Reframe video aspect ratio |
| `video.reframe(start, end, target, mode, callback_url)` | `Video\|None` | 重新调整视频宽高比 |
| `video.clip(prompt, content_type, model_name)` | `str` | Generate clip from prompt (returns stream URL) |
| `video.clip(prompt, content_type, model_name)` | `str` | 根据提示生成片段（返回流 URL） |
| `video.insert_video(video, timestamp)` | `str` | Insert video at timestamp |
| `video.insert_video(video, timestamp)` | `str` | 在时间戳处插入视频 |
| `video.download(name=None)` | `dict` | Download the video |
| `video.download(name=None)` | `dict` | 下载视频 |
| `video.delete()` | `None` | Delete the video |
| `video.delete()` | `None` | 删除视频 |

### Reframe

### Reframe
### 重新构图

Convert a video to a different aspect ratio with optional smart object tracking. Processing is server-side.
将视频转换为不同的宽高比，支持可选的智能对象跟踪。处理在服务器端完成。

> **Warning:** Reframe is a slow server-side operation. It can take several minutes for long videos and may time out. Always use `start`/`end` to limit the segment, or pass `callback_url` for async processing.
> **警告：** Reframe 是一个慢速服务器端操作。长视频可能需要几分钟，并且可能超时。始终使用 `start`/`end` 限制片段，或传入 `callback_url` 进行异步处理。

```python
from videodb import ReframeMode

# Always prefer short segments to avoid timeouts:
reframed = video.reframe(start=0, end=60, target="vertical", mode=ReframeMode.smart)

# Async reframe for full-length videos (returns None, result via webhook):
video.reframe(target="vertical", callback_url="https://example.com/webhook")

# Custom dimensions
reframed = video.reframe(start=0, end=60, target={"width": 1080, "height": 1080})
```

#### reframe Parameters

#### reframe Parameters
#### reframe 参数

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `start` | `float\|None` | `None` | Start time in seconds (None = beginning) |
| `start` | `float\|None` | `None` | 开始时间（秒）（None = 开头） |
| `end` | `float\|None` | `None` | End time in seconds (None = end of video) |
| `end` | `float\|None` | `None` | 结束时间（秒）（None = 视频结尾） |
| `target` | `str\|dict` | `"vertical"` | Preset string (`"vertical"`, `"square"`, `"landscape"`) or `{"width": int, "height": int}` |
| `target` | `str\|dict` | `"vertical"` | 预设字符串（`"vertical"`、`"square"`、`"landscape"`）或 `{"width": int, "height": int}` |
| `mode` | `str` | `ReframeMode.smart` | `"simple"` (centre crop) or `"smart"` (object tracking) |
| `mode` | `str` | `ReframeMode.smart` | `"simple"`（中心裁剪）或 `"smart"`（对象跟踪） |
| `callback_url` | `str\|None` | `None` | Webhook URL for async notification |
| `callback_url` | `str\|None` | `None` | 异步通知的 Webhook URL |

Returns a `Video` object when no `callback_url` is provided, `None` otherwise.
当未提供 `callback_url` 时返回 `Video` 对象，否则返回 `None`。

## Audio Object

## Audio Object
## 音频对象

```python
audio = coll.get_audio(audio_id)
```

### Audio Properties

### Audio Properties
### 音频属性

| Property | Type | Description |
|----------|------|-------------|
| `audio.id` | `str` | Unique audio ID |
| `audio.id` | `str` | 唯一音频 ID |
| `audio.collection_id` | `str` | Parent collection ID |
| `audio.collection_id` | `str` | 父 collection ID |
| `audio.name` | `str` | Audio name |
| `audio.name` | `str` | 音频名称 |
| `audio.length` | `float` | Duration in seconds |
| `audio.length` | `float` | 时长（秒） |

### Audio Methods

### Audio Methods
### 音频方法

| Method | Returns | Description |
|--------|---------|-------------|
| `audio.generate_url()` | `str` | Generate signed URL for playback |
| `audio.generate_url()` | `str` | 生成播放用签名 URL |
| `audio.get_transcript(start=None, end=None)` | `list[dict]` | Get timestamped transcript |
| `audio.get_transcript(start=None, end=None)` | `list[dict]` | 获取带时间戳的转录文本 |
| `audio.get_transcript_text(start=None, end=None)` | `str` | Get full transcript text |
| `audio.get_transcript_text(start=None, end=None)` | `str` | 获取完整转录文本 |
| `audio.generate_transcript(force=None)` | `dict` | Generate transcript |
| `audio.generate_transcript(force=None)` | `dict` | 生成转录文本 |
| `audio.delete()` | `None` | Delete the audio |
| `audio.delete()` | `None` | 删除音频 |

## Image Object

## Image Object
## 图片对象

```python
image = coll.get_image(image_id)
```

### Image Properties

### Image Properties
### 图片属性

| Property | Type | Description |
|----------|------|-------------|
| `image.id` | `str` | Unique image ID |
| `image.id` | `str` | 唯一图片 ID |
| `image.collection_id` | `str` | Parent collection ID |
| `image.collection_id` | `str` | 父 collection ID |
| `image.name` | `str` | Image name |
| `image.name` | `str` | 图片名称 |
| `image.url` | `str\|None` | Image URL (may be `None` for generated images — use `generate_url()` instead) |
| `image.url` | `str\|None` | 图片 URL（生成的图片可能为 `None`——改用 `generate_url()`） |

### Image Methods

### Image Methods
### 图片方法

| Method | Returns | Description |
|--------|---------|-------------|
| `image.generate_url()` | `str` | Generate signed URL |
| `image.generate_url()` | `str` | 生成签名 URL |
| `image.delete()` | `None` | Delete the image |
| `image.delete()` | `None` | 删除图片 |

## Timeline & Editor

## Timeline & Editor
## 时间线和编辑器

### Timeline

### Timeline
### 时间线

```python
from videodb.timeline import Timeline

timeline = Timeline(conn)
```

| Method | Returns | Description |
|--------|---------|-------------|
| `timeline.add_inline(asset)` | `None` | Add `VideoAsset` sequentially on main track |
| `timeline.add_inline(asset)` | `None` | 在主轨上顺序添加 `VideoAsset` |
| `timeline.add_overlay(start, asset)` | `None` | Overlay `AudioAsset`, `ImageAsset`, or `TextAsset` at timestamp |
| `timeline.add_overlay(start, asset)` | `None` | 在时间戳处叠加 `AudioAsset`、`ImageAsset` 或 `TextAsset` |
| `timeline.generate_stream()` | `str` | Compile and get stream URL |
| `timeline.generate_stream()` | `str` | 编译并获取流 URL |

### Asset Types

### Asset Types
### 资产类型

#### VideoAsset

#### VideoAsset
#### 视频资产

```python
from videodb.asset import VideoAsset

asset = VideoAsset(
    asset_id=video.id,
    start=0,              # trim start (seconds)
    end=None,             # trim end (seconds, None = full)
)
```

#### AudioAsset

#### AudioAsset
#### 音频资产

```python
from videodb.asset import AudioAsset

asset = AudioAsset(
    asset_id=audio.id,
    start=0,
    end=None,
    disable_other_tracks=True,   # mute original audio when True
    fade_in_duration=0,          # seconds (max 5)
    fade_out_duration=0,         # seconds (max 5)
)
```

#### ImageAsset

#### ImageAsset
#### 图片资产

```python
from videodb.asset import ImageAsset

asset = ImageAsset(
    asset_id=image.id,
    duration=None,        # display duration (seconds)
    width=100,            # display width
    height=100,           # display height
    x=80,                 # horizontal position (px from left)
    y=20,                 # vertical position (px from top)
)
```

#### TextAsset

#### TextAsset
#### 文本资产

```python
from videodb.asset import TextAsset, TextStyle

asset = TextAsset(
    text="Hello World",
    duration=5,
    style=TextStyle(
        fontsize=24,
        fontcolor="black",
        boxcolor="white",       # background box colour
        alpha=1.0,
        font="Sans",
        text_align="T",         # text alignment within box
    ),
)
```

#### CaptionAsset (Editor API)

#### CaptionAsset (Editor API)
#### 字幕资产（编辑器 API）

CaptionAsset belongs to the Editor API, which has its own Timeline, Track, and Clip system:
CaptionAsset 属于编辑器 API，它有自己的 Timeline、Track 和 Clip 系统：

```python
from videodb.editor import CaptionAsset, FontStyling

asset = CaptionAsset(
    src="auto",                    # "auto" or base64 ASS string
    font=FontStyling(name="Clear Sans", size=30),
    primary_color="&H00FFFFFF",
)
```

See [editor.md](editor.md#caption-overlays) for full CaptionAsset usage with the Editor API.
有关 CaptionAsset 与编辑器 API 的完整用法，参见 [editor.md](editor.md#caption-overlays)。

## Video Search Parameters

## Video Search Parameters
## 视频搜索参数

```python
results = video.search(
    query="your query",
    search_type=SearchType.semantic,       # semantic, keyword, or scene
    index_type=IndexType.spoken_word,      # spoken_word or scene
    result_threshold=None,                 # max number of results
    score_threshold=None,                  # minimum relevance score
    dynamic_score_percentage=None,         # percentage of dynamic score
    scene_index_id=None,                   # target a specific scene index (pass via **kwargs)
    filter=[],                             # metadata filters for scene search
)
```

> **Note:** `filter` is an explicit named parameter in `video.search()`. `scene_index_id` is passed through `**kwargs` to the API.
> **注意：** `filter` 是 `video.search()` 中的显式命名参数。`scene_index_id` 通过 `**kwargs` 传递给 API。
>
> **Important:** `video.search()` raises `InvalidRequestError` with message `"No results found"` when there are no matches. Always wrap search calls in try/except. For scene search, use `score_threshold=0.3` or higher to filter low-relevance noise.
> **重要：** 当没有匹配项时，`video.search()` 会引发消息为 `"No results found"` 的 `InvalidRequestError`。始终将搜索调用包装在 try/except 中。对于场景搜索，使用 `score_threshold=0.3` 或更高来过滤低相关性噪音。

For scene search, use `search_type=SearchType.semantic` with `index_type=IndexType.scene`. Pass `scene_index_id` when targeting a specific scene index. See [search.md](search.md) for details.
对于场景搜索，将 `search_type=SearchType.semantic` 与 `index_type=IndexType.scene` 一起使用。在针对特定场景索引时传递 `scene_index_id`。详情参见 [search.md](search.md)。

## SearchResult Object

## SearchResult Object
## SearchResult 对象

```python
results = video.search("query", search_type=SearchType.semantic)
```

| Method | Returns | Description |
|--------|---------|-------------|
| `results.get_shots()` | `list[Shot]` | Get list of matching segments |
| `results.get_shots()` | `list[Shot]` | 获取匹配片段列表 |
| `results.compile()` | `str` | Compile all shots into a stream URL |
| `results.compile()` | `str` | 将所有片段编译成流 URL |
| `results.play()` | `str` | Open compiled stream in browser |
| `results.play()` | `str` | 在浏览器中打开编译后的流 |

### Shot Properties

### Shot Properties
### Shot 属性

| Property | Type | Description |
|----------|------|-------------|
| `shot.video_id` | `str` | Source video ID |
| `shot.video_id` | `str` | 源视频 ID |
| `shot.video_length` | `float` | Source video duration |
| `shot.video_length` | `float` | 源视频时长 |
| `shot.video_title` | `str` | Source video title |
| `shot.video_title` | `str` | 源视频标题 |
| `shot.start` | `float` | Start time (seconds) |
| `shot.start` | `float` | 开始时间（秒） |
| `shot.end` | `float` | End time (seconds) |
| `shot.end` | `float` | 结束时间（秒） |
| `shot.text` | `str` | Matched text content |
| `shot.text` | `str` | 匹配的文本内容 |
| `shot.search_score` | `float` | Search relevance score |
| `shot.search_score` | `float` | 搜索相关性得分 |

| Method | Returns | Description |
|--------|---------|-------------|
| `shot.generate_stream()` | `str` | Stream this specific shot |
| `shot.generate_stream()` | `str` | 流式传输此特定片段 |
| `shot.play()` | `str` | Open shot stream in browser |
| `shot.play()` | `str` | 在浏览器中打开片段流 |

## Meeting Object

## Meeting Object
## 会议对象

```python
meeting = coll.record_meeting(
    meeting_url="https://meet.google.com/...",
    bot_name="Bot",
    callback_url=None,          # Webhook URL for status updates
    callback_data=None,         # Optional dict passed through to callbacks
    time_zone="UTC",            # Time zone for the meeting
)
```

### Meeting Properties

### Meeting Properties
### 会议属性

| Property | Type | Description |
|----------|------|-------------|
| `meeting.id` | `str` | Unique meeting ID |
| `meeting.id` | `str` | 唯一会议 ID |
| `meeting.collection_id` | `str` | Parent collection ID |
| `meeting.collection_id` | `str` | 父 collection ID |
| `meeting.status` | `str` | Current status |
| `meeting.status` | `str` | 当前状态 |
| `meeting.video_id` | `str` | Recorded video ID (after completion) |
| `meeting.video_id` | `str` | 录制的视频 ID（完成后） |
| `meeting.bot_name` | `str` | Bot name |
| `meeting.bot_name` | `str` | 机器人名称 |
| `meeting.meeting_title` | `str` | Meeting title |
| `meeting.meeting_title` | `str` | 会议标题 |
| `meeting.meeting_url` | `str` | Meeting URL |
| `meeting.meeting_url` | `str` | 会议 URL |
| `meeting.speaker_timeline` | `dict` | Speaker timeline data |
| `meeting.speaker_timeline` | `dict` | 发言人时间线数据 |
| `meeting.is_active` | `bool` | True if initializing or processing |
| `meeting.is_active` | `bool` | 如果正在初始化或处理则为 True |
| `meeting.is_completed` | `bool` | True if done |
| `meeting.is_completed` | `bool` | 如果完成则为 True |

### Meeting Methods

### Meeting Methods
### 会议方法

| Method | Returns | Description |
|--------|---------|-------------|
| `meeting.refresh()` | `Meeting` | Refresh data from server |
| `meeting.refresh()` | `Meeting` | 从服务器刷新数据 |
| `meeting.wait_for_status(target_status, timeout=14400, interval=120)` | `bool` | Poll until status reached |
| `meeting.wait_for_status(target_status, timeout=14400, interval=120)` | `bool` | 轮询直到达到目标状态 |

## RTStream & Capture

## RTStream & Capture
## RTStream 和捕获

For RTStream (live ingestion, indexing, transcription), see [rtstream-reference.md](rtstream-reference.md).
关于 RTStream（实时摄入、索引、转录），参见 [rtstream-reference.md](rtstream-reference.md)。

For capture sessions (desktop recording, CaptureClient, channels), see [capture-reference.md](capture-reference.md).
关于捕获会话（桌面录制、CaptureClient、通道），参见 [capture-reference.md](capture-reference.md)。

## Enums & Constants

## Enums & Constants
## 枚举和常量

### SearchType

### SearchType
### 搜索类型

```python
from videodb import SearchType

SearchType.semantic    # Natural language semantic search
SearchType.keyword     # Exact keyword matching
SearchType.scene       # Visual scene search (may require paid plan)
SearchType.llm         # LLM-powered search
```

### SceneExtractionType

### SceneExtractionType
### 场景提取类型

```python
from videodb import SceneExtractionType

SceneExtractionType.shot_based   # Automatic shot boundary detection
SceneExtractionType.time_based   # Fixed time interval extraction
SceneExtractionType.transcript   # Transcript-based scene extraction
```

### SubtitleStyle

### SubtitleStyle
### 字幕样式

```python
from videodb import SubtitleStyle

style = SubtitleStyle(
    font_name="Arial",
    font_size=18,
    primary_colour="&H00FFFFFF",
    bold=False,
    # ... see SubtitleStyle for all options
)
video.add_subtitle(style=style)
```

### SubtitleAlignment & SubtitleBorderStyle

### SubtitleAlignment & SubtitleBorderStyle
### 字幕对齐和字幕边框样式

```python
from videodb import SubtitleAlignment, SubtitleBorderStyle
```

### TextStyle

### TextStyle
### 文本样式

```python
from videodb import TextStyle
# or: from videodb.asset import TextStyle

style = TextStyle(
    fontsize=24,
    fontcolor="black",
    boxcolor="white",
    font="Sans",
    text_align="T",
    alpha=1.0,
)
```

### Other Constants

### Other Constants
### 其他常量

```python
from videodb import (
    IndexType,          # spoken_word, scene
    MediaType,          # video, audio, image
    Segmenter,          # word, sentence, time
    SegmentationType,   # sentence, llm
    TranscodeMode,      # economy, lightning
    ResizeMode,         # crop, fit, pad
    ReframeMode,        # simple, smart
    RTStreamChannelType,
)
```

## Exceptions

## Exceptions
## 异常

```python
from videodb.exceptions import (
    AuthenticationError,     # Invalid or missing API key
    InvalidRequestError,     # Bad parameters or malformed request
    RequestTimeoutError,     # Request timed out
    SearchError,             # Search operation failure (e.g. not indexed)
    VideodbError,            # Base exception for all VideoDB errors
)
```

| Exception | Common Cause |
|-----------|-------------|
| `AuthenticationError` | Missing or invalid `VIDEO_DB_API_KEY` |
| `AuthenticationError` | 缺少或无效的 `VIDEO_DB_API_KEY` |
| `InvalidRequestError` | Invalid URL, unsupported format, bad parameters |
| `InvalidRequestError` | 无效 URL、不支持的格式、错误参数 |
| `RequestTimeoutError` | Server took too long to respond |
| `RequestTimeoutError` | 服务器响应时间过长 |
| `SearchError` | Searching before indexing, invalid search type |
| `SearchError` | 索引前搜索、无效的搜索类型 |
| `VideodbError` | Server errors, network issues, generic failures |
| `VideodbError` | 服务器错误、网络问题、通用故障 |
