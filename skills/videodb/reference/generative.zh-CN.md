# Generative Media Guide

## Generative Media Guide
## 生成媒体指南

VideoDB provides AI-powered generation of images, videos, music, sound effects, voice, and text content. All generation methods are on the **Collection** object.
VideoDB 提供 AI 驱动的图片、视频、音乐、音效、语音和文本内容生成。所有生成方法都在 **Collection** 对象上。

## Prerequisites

## Prerequisites
## 前提条件

You need a connection and a collection reference before calling any generation method:
在调用任何生成方法之前，您需要连接和 collection 引用：

```python
import videodb

conn = videodb.connect()
coll = conn.get_collection()
```

## Image Generation

## Image Generation
## 图片生成

Generate images from text prompts:
从文本提示生成图片：

```python
image = coll.generate_image(
    prompt="a futuristic cityscape at sunset with flying cars",
    aspect_ratio="16:9",
)

# Access the generated image
print(image.id)
print(image.generate_url())  # returns a signed download URL
```

### generate_image Parameters

### generate_image Parameters
### generate_image 参数

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `prompt` | `str` | required | Text description of the image to generate |
| `prompt` | `str` | 必需 | 要生成的图片的文本描述 |
| `aspect_ratio` | `str` | `"1:1"` | Aspect ratio: `"1:1"`, `"9:16"`, `"16:9"`, `"4:3"`, or `"3:4"` |
| `aspect_ratio` | `str` | `"1:1"` | 宽高比：`"1:1"`、`"9:16"`、`"16:9"`、`"4:3"` 或 `"3:4"` |
| `callback_url` | `str\|None` | `None` | URL to receive async callback |
| `callback_url` | `str\|None` | `None` | 接收异步回调的 URL |

Returns an `Image` object with `.id`, `.name`, and `.collection_id`. The `.url` property may be `None` for generated images — always use `image.generate_url()` to get a reliable signed download URL.
返回具有 `.id`、`.name` 和 `.collection_id` 的 `Image` 对象。对于生成的图片，`.url` 属性可能为 `None`——始终使用 `image.generate_url()` 获取可靠的签名下载 URL。

> **Note:** Unlike `Video` objects (which use `.generate_stream()`), `Image` objects use `.generate_url()` to retrieve the image URL. The `.url` property is only populated for some image types (e.g. thumbnails).
> **注意：** 与使用 `.generate_stream()` 的 `Video` 对象不同，`Image` 对象使用 `.generate_url()` 来获取图片 URL。`.url` 属性仅对某些图片类型（例如缩略图）填充。

## Video Generation

## Video Generation
## 视频生成

Generate short video clips from text prompts:
从文本提示生成短视频片段：

```python
video = coll.generate_video(
    prompt="a timelapse of a flower blooming in a garden",
    duration=5,
)

stream_url = video.generate_stream()
video.play()
```

### generate_video Parameters

### generate_video Parameters
### generate_video 参数

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `prompt` | `str` | required | Text description of the video to generate |
| `prompt` | `str` | 必需 | 要生成的视频的文本描述 |
| `duration` | `int` | `5` | Duration in seconds (must be integer value, 5-8) |
| `duration` | `int` | `5` | 时长（秒）（必须为整数值，5-8） |
| `callback_url` | `str\|None` | `None` | URL to receive async callback |
| `callback_url` | `str\|None` | `None` | 接收异步回调的 URL |

Returns a `Video` object. Generated videos are automatically added to the collection and can be used in timelines, searches, and compilations like any uploaded video.
返回 `Video` 对象。生成的视频会自动添加到 collection 中，可以像任何上传的视频一样在时间线、搜索和组合中使用。

## Audio Generation

## Audio Generation
## 音频生成

VideoDB provides three separate methods for different audio types.
VideoDB 为不同的音频类型提供三种单独的方法。

### Music

### Music
### 音乐

Generate background music from text descriptions:
从文本描述生成背景音乐：

```python
music = coll.generate_music(
    prompt="upbeat electronic music with a driving beat, suitable for a tech demo",
    duration=30,
)

print(music.id)
```

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `prompt` | `str` | required | Text description of the music |
| `prompt` | `str` | 必需 | 音乐的文本描述 |
| `duration` | `int` | `5` | Duration in seconds |
| `duration` | `int` | `5` | 时长（秒） |
| `callback_url` | `str\|None` | `None` | URL to receive async callback |
| `callback_url` | `str\|None` | `None` | 接收异步回调的 URL |

### Sound Effects

### Sound Effects
### 音效

Generate specific sound effects:
生成特定音效：

```python
sfx = coll.generate_sound_effect(
    prompt="thunderstorm with heavy rain and distant thunder",
    duration=10,
)
```

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `prompt` | `str` | required | Text description of the sound effect |
| `prompt` | `str` | 必需 | 音效的文本描述 |
| `duration` | `int` | `2` | Duration in seconds |
| `duration` | `int` | `2` | 时长（秒） |
| `config` | `dict` | `{}` | Additional configuration |
| `config` | `dict` | `{}` | 附加配置 |
| `callback_url` | `str\|None` | `None` | URL to receive async callback |
| `callback_url` | `str\|None` | `None` | 接收异步回调的 URL |

### Voice (Text-to-Speech)

### Voice (Text-to-Speech)
### 语音（文本转语音）

Generate speech from text:
从文本生成语音：

```python
voice = coll.generate_voice(
    text="Welcome to our product demo. Today we'll walk through the key features.",
    voice_name="Default",
)
```

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `text` | `str` | required | Text to convert to speech |
| `text` | `str` | 必需 | 要转换为语音的文本 |
| `voice_name` | `str` | `"Default"` | Voice to use |
| `voice_name` | `str` | `"Default"` | 要使用的语音 |
| `config` | `dict` | `{}` | Additional configuration |
| `config` | `dict` | `{}` | 附加配置 |
| `callback_url` | `str\|None` | `None` | URL to receive async callback |
| `callback_url` | `str\|None` | `None` | 接收异步回调的 URL |

All three audio methods return an `Audio` object with `.id`, `.name`, `.length`, and `.collection_id`.
所有三种音频方法都返回具有 `.id`、`.name`、`.length` 和 `.collection_id` 的 `Audio` 对象。

## Text Generation (LLM Integration)

## Text Generation (LLM Integration)
## 文本生成（LLM 集成）

Use `coll.generate_text()` to run LLM analysis. This is a **Collection-level** method -- pass any context (transcripts, descriptions) directly in the prompt string.
使用 `coll.generate_text()` 运行 LLM 分析。这是一个**Collection 级别**的方法——直接在提示字符串中传递任何上下文（转录文本、描述）。

```python
# Get transcript from a video first
transcript_text = video.get_transcript_text()

# Generate analysis using collection LLM
result = coll.generate_text(
    prompt=f"Summarize the key points discussed in this video:\n{transcript_text}",
    model_name="pro",
)

print(result["output"])
```

### generate_text Parameters

### generate_text Parameters
### generate_text 参数

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `prompt` | `str` | required | Prompt with context for the LLM |
| `prompt` | `str` | 必需 | 带 LLM 上下文的提示 |
| `model_name` | `str` | `"basic"` | Model tier: `"basic"`, `"pro"`, or `"ultra"` |
| `model_name` | `str` | `"basic"` | 模型层级：`"basic"`、`"pro"` 或 `"ultra"` |
| `response_type` | `str` | `"text"` | Response format: `"text"` or `"json"` |
| `response_type` | `str` | `"text"` | 响应格式：`"text"` 或 `"json"` |

Returns a `dict` with an `output` key. When `response_type="text"`, `output` is a `str`. When `response_type="json"`, `output` is a `dict`.
返回带有 `output` 键的 `dict`。当 `response_type="text"` 时，`output` 是 `str`。当 `response_type="json"` 时，`output` 是 `dict`。

```python
result = coll.generate_text(prompt="Summarize this", model_name="pro")
print(result["output"])  # access the actual text/dict
```

### Analyze Scenes with LLM

### Analyze Scenes with LLM
### 使用 LLM 分析场景

Combine scene extraction with text generation:
将场景提取与文本生成结合：

```python
from videodb import SceneExtractionType

# First index scenes
scenes = video.index_scenes(
    extraction_type=SceneExtractionType.time_based,
    extraction_config={"time": 10},
    prompt="Describe the visual content in this scene.",
)

# Get transcript for spoken context
transcript_text = video.get_transcript_text()
scene_descriptions = []
for scene in scenes:
    if isinstance(scene, dict):
        description = scene.get("description") or scene.get("summary")
    else:
        description = getattr(scene, "description", None) or getattr(scene, "summary", None)
    scene_descriptions.append(description or str(scene))

scenes_text = "\n".join(scene_descriptions)

# Analyze with collection LLM
result = coll.generate_text(
    prompt=(
        f"Given this video transcript:\n{transcript_text}\n\n"
        f"And these visual scene descriptions:\n{scenes_text}\n\n"
        "Based on the spoken and visual content, describe the main topics covered."
    ),
    model_name="pro",
)
print(result["output"])
```

## Dubbing and Translation

## Dubbing and Translation
## 配音和翻译

### Dub a Video

### Dub a Video
### 为视频配音

Dub a video into another language using the collection method:
使用 collection 方法将视频配音为另一种语言：

```python
dubbed_video = coll.dub_video(
    video_id=video.id,
    language_code="es",  # Spanish
)

dubbed_video.play()
```

### dub_video Parameters

### dub_video Parameters
### dub_video 参数

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `video_id` | `str` | required | ID of the video to dub |
| `video_id` | `str` | 必需 | 要配音的视频 ID |
| `language_code` | `str` | required | Target language code (e.g., `"es"`, `"fr"`, `"de"`) |
| `language_code` | `str` | 必需 | 目标语言代码（例如 `"es"`、`"fr"`、`"de"`） |
| `callback_url` | `str\|None` | `None` | URL to receive async callback |
| `callback_url` | `str\|None` | `None` | 接收异步回调的 URL |

Returns a `Video` object with the dubbed content.
返回带有配音内容的 `Video` 对象。

### Translate Transcript

### Translate Transcript
### 翻译转录文本

Translate a video's transcript without dubbing:
翻译视频转录文本而不配音：

```python
translated = video.translate_transcript(
    language="Spanish",
    additional_notes="Use formal tone",
)

for entry in translated:
    print(entry)
```

**Supported languages** include: `en`, `es`, `fr`, `de`, `it`, `pt`, `ja`, `ko`, `zh`, `hi`, `ar`, and more.
**支持的语言**包括：`en`、`es`、`fr`、`de`、`it`、`pt`、`ja`、`ko`、`zh`、`hi`、`ar` 等。

## Complete Workflow Examples

## Complete Workflow Examples
## 完整工作流示例

### Generate Narration for a Video

### Generate Narration for a Video
### 为视频生成配音

```python
import videodb

conn = videodb.connect()
coll = conn.get_collection()
video = coll.get_video("your-video-id")

# Get transcript
transcript_text = video.get_transcript_text()

# Generate narration script using collection LLM
result = coll.generate_text(
    prompt=(
        f"Write a professional narration script for this video content:\n"
        f"{transcript_text[:2000]}"
    ),
    model_name="pro",
)
script = result["output"]

# Convert script to speech
narration = coll.generate_voice(text=script)
print(f"Narration audio: {narration.id}")
```

### Generate Thumbnail from Prompt

### Generate Thumbnail from Prompt
### 从提示生成缩略图

```python
thumbnail = coll.generate_image(
    prompt="professional video thumbnail showing data analytics dashboard, modern design",
    aspect_ratio="16:9",
)
print(f"Thumbnail URL: {thumbnail.generate_url()}")
```

### Add Generated Music to Video

### Add Generated Music to Video
### 向视频添加生成的音乐

```python
import videodb
from videodb.timeline import Timeline
from videodb.asset import VideoAsset, AudioAsset

conn = videodb.connect()
coll = conn.get_collection()
video = coll.get_video("your-video-id")

# Generate background music
music = coll.generate_music(
    prompt="calm ambient background music for a tutorial video",
    duration=60,
)

# Build timeline with video + music overlay
timeline = Timeline(conn)
timeline.add_inline(VideoAsset(asset_id=video.id))
timeline.add_overlay(0, AudioAsset(asset_id=music.id, disable_other_tracks=False))

stream_url = timeline.generate_stream()
print(f"Video with music: {stream_url}")
```

### Structured JSON Output

### Structured JSON Output
### 结构化 JSON 输出

```python
transcript_text = video.get_transcript_text()

result = coll.generate_text(
    prompt=(
        f"Given this transcript:\n{transcript_text}\n\n"
        "Return a JSON object with keys: summary, topics (array), action_items (array)."
    ),
    model_name="pro",
    response_type="json",
)

# result["output"] is a dict when response_type="json"
print(result["output"]["summary"])
print(result["output"]["topics"])
```

## Tips

## Tips
## 提示

- **Generated media is persistent**: All generated content is stored in your collection and can be reused.
  - **生成的媒体是持久化的**：所有生成的内容都存储在您的 collection 中，可以重复使用。
- **Three audio methods**: Use `generate_music()` for background music, `generate_sound_effect()` for SFX, and `generate_voice()` for text-to-speech. There is no unified `generate_audio()` method.
  - **三种音频方法**：背景音乐使用 `generate_music()`，音效使用 `generate_sound_effect()`，文本转语音使用 `generate_voice()`。没有统一的 `generate_audio()` 方法。
- **Text generation is collection-level**: `coll.generate_text()` does not have access to video content automatically. Fetch the transcript with `video.get_transcript_text()` and pass it in the prompt.
  - **文本生成是 Collection 级别的**：`coll.generate_text()` 不会自动访问视频内容。使用 `video.get_transcript_text()` 获取转录文本并将其传递给提示。
- **Model tiers**: `"basic"` is fastest, `"pro"` is balanced, `"ultra"` is highest quality. Use `"pro"` for most analysis tasks.
  - **模型层级**：`"basic"` 最快，`"pro"` 平衡，`"ultra"` 质量最高。对大多数分析任务使用 `"pro"`。
- **Combine generation types**: Generate images for overlays, music for backgrounds, and voice for narration, then compose using timelines (see [editor.md](editor.md)).
  - **组合生成类型**：为叠加层生成图片，为背景生成音乐，为配音生成语音，然后使用时间线组合（参见 [editor.md](editor.md)）。
- **Prompt quality matters**: Descriptive, specific prompts produce better results across all generation types.
  - **提示质量很重要**：描述性、具体的提示在所有生成类型中都能产生更好的结果。
- **Aspect ratios for images**: Choose from `"1:1"`, `"9:16"`, `"16:9"`, `"4:3"`, or `"3:4"`.
  - **图片宽高比**：从 `"1:1"`、`"9:16"`、`"16:9"`、`"4:3"` 或 `"3:4"` 中选择。
