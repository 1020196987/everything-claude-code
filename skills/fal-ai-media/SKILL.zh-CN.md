---
name: fal-ai-media
description: Unified media generation via fal.ai MCP — image, video, and audio. Covers text-to-image (Nano Banana), text/image-to-video (Seedance, Kling, Veo 3), text-to-speech (CSM-1B), and video-to-audio (ThinkSound). Use when the user wants to generate images, videos, or audio with AI.
description zh-CN: 通过fal.ai MCP统一生成媒体——图像、视频和音频。涵盖文生图（Nano Banana）、文/图生视频（Seedance、Kling、Veo 3）、语音合成（CSM-1B）和视频转音频（ThinkSound）。当用户想用AI生成图像、视频或音频时使用。
origin: ECC
---

# fal.ai Media Generation

## fal.ai Media Generation
## fal.ai 媒体生成

Generate images, videos, and audio using fal.ai models via MCP.
通过MCP使用fal.ai模型生成图像、视频和音频。

## When to Activate

## When to Activate
## 何时激活

- User wants to generate images from text prompts
  用户想从文本提示生成图像
- Creating videos from text or images
  从文本或图像创建视频
- Generating speech, music, or sound effects
  生成语音、音乐或音效
- Any media generation task
  任何媒体生成任务
- User says "generate image", "create video", "text to speech", "make a thumbnail", or similar
  用户说"生成图像"、"创建视频"、"语音合成"、"制作缩略图"或类似的话

## MCP Requirement

## MCP Requirement
## MCP要求

fal.ai MCP server must be configured. Add to `~/.claude.json`:
必须配置fal.ai MCP服务器。添加到`~/.claude.json`：

```json
"fal-ai": {
  "command": "npx",
  "args": ["-y", "fal-ai-mcp-server"],
  "env": { "FAL_KEY": "YOUR_FAL_KEY_HERE" }
}
```

Get an API key at [fal.ai](https://fal.ai).
在[fal.ai](https://fal.ai)获取API密钥。

## MCP Tools

## MCP Tools
## MCP工具

The fal.ai MCP provides these tools:
fal.ai MCP提供这些工具：

- `search` — Find available models by keyword
  `search` — 按关键字查找可用模型
- `find` — Get model details and parameters
  `find` — 获取模型详情和参数
- `generate` — Run a model with parameters
  `generate` — 用参数运行模型
- `result` — Check async generation status
  `result` — 检查异步生成状态
- `status` — Check job status
  `status` — 检查作业状态
- `cancel` — Cancel a running job
  `cancel` — 取消正在运行的作业
- `estimate_cost` — Estimate generation cost
  `estimate_cost` — 估算生成成本
- `models` — List popular models
  `models` — 列出流行模型
- `upload` — Upload files for use as inputs
  `upload` — 上传文件作为输入

---

## Image Generation

## Image Generation
## 图像生成

### Nano Banana 2 (Fast)

Best for: quick iterations, drafts, text-to-image, image editing.
最佳用途：快速迭代、草稿、文生图、图像编辑。

```
generate(
  app_id: "fal-ai/nano-banana-2",
  input_data: {
    "prompt": "a futuristic cityscape at sunset, cyberpunk style",
    "image_size": "landscape_16_9",
    "num_images": 1,
    "seed": 42
  }
)
```

### Nano Banana Pro (High Fidelity)

Best for: production images, realism, typography, detailed prompts.
最佳用途：生产图像、逼真度、排版、详细提示。

```
generate(
  app_id: "fal-ai/nano-banana-pro",
  input_data: {
    "prompt": "professional product photo of wireless headphones on marble surface, studio lighting",
    "image_size": "square",
    "num_images": 1,
    "guidance_scale": 7.5
  }
)
```

### Common Image Parameters

### Common Image Parameters
### 常见图像参数

| Param | Type | Options | Notes |
|-------|------|---------|-------|
| `prompt` | string | required | Describe what you want |
| `image_size` | string | `square`, `portrait_4_3`, `landscape_16_9`, `portrait_16_9`, `landscape_4_3` | Aspect ratio |
| `num_images` | number | 1-4 | How many to generate |
| `seed` | number | any integer | Reproducibility |
| `guidance_scale` | number | 1-20 | How closely to follow the prompt (higher = more literal) |

### Image Editing

### Image Editing
### 图像编辑

Use Nano Banana 2 with an input image for inpainting, outpainting, or style transfer:
使用Nano Banana 2配合输入图像进行修复、外绘或风格迁移：

```
# First upload the source image
upload(file_path: "/path/to/image.png")

# Then generate with image input
generate(
  app_id: "fal-ai/nano-banana-2",
  input_data: {
    "prompt": "same scene but in watercolor style",
    "image_url": "<uploaded_url>",
    "image_size": "landscape_16_9"
  }
)
```

---

## Video Generation

## Video Generation
## 视频生成

### Seedance 1.0 Pro (ByteDance)

Best for: text-to-video, image-to-video with high motion quality.
最佳用途：文生视频、高运动质量的图生视频。

```
generate(
  app_id: "fal-ai/seedance-1-0-pro",
  input_data: {
    "prompt": "a drone flyover of a mountain lake at golden hour, cinematic",
    "duration": "5s",
    "aspect_ratio": "16:9",
    "seed": 42
  }
)
```

### Kling Video v3 Pro

Best for: text/image-to-video with native audio generation.
最佳用途：带原生音频生成的文/图生视频。

```
generate(
  app_id: "fal-ai/kling-video/v3/pro",
  input_data: {
    "prompt": "ocean waves crashing on a rocky coast, dramatic clouds",
    "duration": "5s",
    "aspect_ratio": "16:9"
  }
)
```

### Veo 3 (Google DeepMind)

Best for: video with generated sound, high visual quality.
最佳用途：带生成声音、高视觉质量的视频。

```
generate(
  app_id: "fal-ai/veo-3",
  input_data: {
    "prompt": "a bustling Tokyo street market at night, neon signs, crowd noise",
    "aspect_ratio": "16:9"
  }
)
```

### Image-to-Video

Start from an existing image:
从现有图像开始：

```
generate(
  app_id: "fal-ai/seedance-1-0-pro",
  input_data: {
    "prompt": "camera slowly zooms out, gentle wind moves the trees",
    "image_url": "<uploaded_image_url>",
    "duration": "5s"
  }
)
```

### Video Parameters

### Video Parameters
### 视频参数

| Param | Type | Options | Notes |
|-------|------|---------|-------|
| `prompt` | string | required | Describe the video |
| `duration` | string | `"5s"`, `"10s"` | Video length |
| `aspect_ratio` | string | `"16:9"`, `"9:16"`, `"1:1"` | Frame ratio |
| `seed` | number | any integer | Reproducibility |
| `image_url` | string | URL | Source image for image-to-video |

---

## Audio Generation

## Audio Generation
## 音频生成

### CSM-1B (Conversational Speech)

Text-to-speech with natural, conversational quality.
具有自然对话质量的语音合成。

```
generate(
  app_id: "fal-ai/csm-1b",
  input_data: {
    "text": "Hello, welcome to the demo. Let me show you how this works.",
    "speaker_id": 0
  }
)
```

### ThinkSound (Video-to-Audio)

Generate matching audio from video content.
从视频内容生成匹配的音频。

```
generate(
  app_id: "fal-ai/thinksound",
  input_data: {
    "video_url": "<video_url>",
    "prompt": "ambient forest sounds with birds chirping"
  }
)
```

### ElevenLabs (via API, no MCP)

For professional voice synthesis, use ElevenLabs directly:
对于专业语音合成，直接使用ElevenLabs：

```python
import os
import requests

resp = requests.post(
    "https://api.elevenlabs.io/v1/text-to-speech/<voice_id>",
    headers={
        "xi-api-key": os.environ["ELEVENLABS_API_KEY"],
        "Content-Type": "application/json"
    },
    json={
        "text": "Your text here",
        "model_id": "eleven_turbo_v2_5",
        "voice_settings": {"stability": 0.5, "similarity_boost": 0.75}
    }
)
with open("output.mp3", "wb") as f:
    f.write(resp.content)
```

### VideoDB Generative Audio

If VideoDB is configured, use its generative audio:
如果配置了VideoDB，使用其生成音频：

```python
# Voice generation
audio = coll.generate_voice(text="Your narration here", voice="alloy")

# Music generation
music = coll.generate_music(prompt="upbeat electronic background music", duration=30)

# Sound effects
sfx = coll.generate_sound_effect(prompt="thunder crack followed by rain")
```

---

## Cost Estimation

## Cost Estimation
## 成本估算

Before generating, check estimated cost:
生成前检查估算成本：

```
estimate_cost(
  estimate_type: "unit_price",
  endpoints: {
    "fal-ai/nano-banana-pro": {
      "unit_quantity": 1
    }
  }
)
```

## Model Discovery

## Model Discovery
## 模型发现

Find models for specific tasks:
查找特定任务的模型：

```
search(query: "text to video")
find(endpoint_ids: ["fal-ai/seedance-1-0-pro"])
models()
```

## Tips

## Tips
## 提示

- Use `seed` for reproducible results when iterating on prompts
  在迭代提示时使用`seed`获得可重复的结果
- Start with lower-cost models (Nano Banana 2) for prompt iteration, then switch to Pro for finals
  从低成本模型（Nano Banana 2）开始进行提示迭代，然后用Pro版做最终成品
- For video, keep prompts descriptive but concise — focus on motion and scene
  对于视频，保持提示描述性但简洁——专注于运动和场景
- Image-to-video produces more controlled results than pure text-to-video
  图生视频比纯文生视频产生更可控的结果
- Check `estimate_cost` before running expensive video generations
  在运行昂贵的视频生成之前检查`estimate_cost`

## Related Skills

## Related Skills
## 相关技能

- `videodb` — Video processing, editing, and streaming
  `videodb` — 视频处理、编辑和流媒体
- `video-editing` — AI-powered video editing workflows
  `video-editing` — AI驱动的视频编辑工作流
- `content-engine` — Content creation for social platforms
  `content-engine` — 社交平台内容创作
