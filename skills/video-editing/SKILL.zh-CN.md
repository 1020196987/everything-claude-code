---
name: video-editing
description: AI-assisted video editing workflows for cutting, structuring, and augmenting real footage. Covers the full pipeline from raw capture through FFmpeg, Remotion, ElevenLabs, fal.ai, and final polish in Descript or CapCut. Use when the user wants to edit video, cut footage, create vlogs, or build video content.
description zh-CN: AI 辅助的视频编辑工作流，用于剪切、构建和增强真实素材。涵盖从原始素材到 FFmpeg、Remotion、ElevenLabs、fal.ai，再到 Descript 或 CapCut 最终精修的完整流程。当用户想编辑视频、剪切素材、创建 vlog 或构建视频内容时使用。
origin: ECC
---

# Video Editing

## Video Editing
## 视频编辑

AI-assisted editing for real footage. Not generation from prompts. Editing existing video fast.

真实素材的 AI 辅助编辑。不是从提示生成。快速编辑现有视频。

## When to Activate

## When to Activate
## 何时激活

- User wants to edit, cut, or structure video footage
  - 用户想编辑、剪切或构建视频素材
- Turning long recordings into short-form content
  - 将长录制转换为短视频内容
- Building vlogs, tutorials, or demo videos from raw capture
  - 从原始素材构建 vlog、教程或演示视频
- Adding overlays, subtitles, music, or voiceover to existing video
  - 向现有视频添加叠加层、字幕、音乐或配音
- Reframing video for different platforms (YouTube, TikTok, Instagram)
  - 为不同平台（YouTube、TikTok、Instagram）重新构图视频
- User says "edit video", "cut this footage", "make a vlog", or "video workflow"
  - 用户说"编辑视频"、"剪切这段素材"、"制作 vlog"或"视频工作流"

## Core Thesis

## Core Thesis
## 核心论点

AI video editing is useful when you stop asking it to create the whole video and start using it to compress, structure, and augment real footage. The value is not generation. The value is compression.

当你停止让 AI 创建整个视频，而是开始用它来压缩、构建和增强真实素材时，AI 视频编辑才有用。价值不在于生成。价值在于压缩。

## The Pipeline

## The Pipeline
## 流程

```
Screen Studio / raw footage
  → Claude / Codex
  → FFmpeg
  → Remotion
  → ElevenLabs / fal.ai
  → Descript or CapCut
```

Each layer has a specific job. Do not skip layers. Do not try to make one tool do everything.

每一层都有特定的工作。不要跳过层次。不要试图让一个工具做所有事情。

## Layer 1: Capture (Screen Studio / Raw Footage)

## Layer 1: Capture (Screen Studio / Raw Footage)
## 层级 1：采集（Screen Studio / 原始素材）

Collect the source material:

收集源素材：
- **Screen Studio**: polished screen recordings for app demos, coding sessions, browser workflows
  - **Screen Studio**：应用演示、编码会话、浏览器工作流的精致屏幕录制
- **Raw camera footage**: vlog footage, interviews, event recordings
  - **原始摄像机素材**：vlog 素材、访谈、活动录制
- **Desktop capture via VideoDB**: session recording with real-time context (see `videodb` skill)
  - **通过 VideoDB 桌面采集**：带实时上下文的会话录制（见 `videodb` skill）

Output: raw files ready for organization.

输出：准备组织的原始文件。

## Layer 2: Organization (Claude / Codex)

## Layer 2: Organization (Claude / Codex)
## 层级 2：组织（Claude / Codex）

Use Claude Code or Codex to:
使用 Claude Code 或 Codex：
- **Transcribe and label**: generate transcript, identify topics and themes
  - **转录和标记**：生成转录本，识别主题
- **Plan structure**: decide what stays, what gets cut, what order works
  - **规划结构**：决定保留什么、剪切什么、什么顺序有效
- **Identify dead sections**: find pauses, tangents, repeated takes
  - **识别无用片段**：找出停顿、离题、重复镜头
- **Generate edit decision list**: timestamps for cuts, segments to keep
  - **生成剪辑决策列表**：剪切的时间戳、要保留的片段
- **Scaffold FFmpeg and Remotion code**: generate the commands and compositions
  - **搭建 FFmpeg 和 Remotion 代码**：生成命令和合成

```
Example prompt:
"Here's the transcript of a 4-hour recording. Identify the 8 strongest segments
for a 24-minute vlog. Give me FFmpeg cut commands for each segment."
```

This layer is about structure, not final creative taste.

这一层关乎结构，而非最终创意品味。

## Layer 3: Deterministic Cuts (FFmpeg)

## Layer 3: Deterministic Cuts (FFmpeg)
## 层级 3：确定性剪切（FFmpeg）

FFmpeg handles the boring but critical work: splitting, trimming, concatenating, and preprocessing.

FFmpeg 处理无聊但关键的工作：分割、修剪、连接和预处理。

### Extract segment by timestamp

### Extract segment by timestamp
### 按时间戳提取片段

```bash
ffmpeg -i raw.mp4 -ss 00:12:30 -to 00:15:45 -c copy segment_01.mp4
```

### Batch cut from edit decision list

### Batch cut from edit decision list
### 从剪辑决策列表批量剪切

```bash
#!/bin/bash
# cuts.txt: start,end,label
while IFS=, read -r start end label; do
  ffmpeg -i raw.mp4 -ss "$start" -to "$end" -c copy "segments/${label}.mp4"
done < cuts.txt
```

### Concatenate segments

### Concatenate segments
### 连接片段

```bash
# Create file list
for f in segments/*.mp4; do echo "file '$f'"; done > concat.txt
ffmpeg -f concat -safe 0 -i concat.txt -c copy assembled.mp4
```

### Create proxy for faster editing

### Create proxy for faster editing
### 为更快编辑创建代理

```bash
ffmpeg -i raw.mp4 -vf "scale=960:-2" -c:v libx264 -preset ultrafast -crf 28 proxy.mp4
```

### Extract audio for transcription

### Extract audio for transcription
### 提取音频用于转录

```bash
ffmpeg -i raw.mp4 -vn -acodec pcm_s16le -ar 16000 audio.wav
```

### Normalize audio levels

### Normalize audio levels
### 标准化音频电平

```bash
ffmpeg -i segment.mp4 -af loudnorm=I=-16:TP=-1.5:LRA=11 -c:v copy normalized.mp4
```

## Layer 4: Programmable Composition (Remotion)

## Layer 4: Programmable Composition (Remotion)
## 层级 4：可编程合成（Remotion）

Remotion turns editing problems into composable code. Use it for things that traditional editors make painful:

Remotion 将编辑问题转化为可组合的代码。用于传统编辑器难以处理的事项：

### When to use Remotion

### When to use Remotion
### 何时使用 Remotion

- Overlays: text, images, branding, lower thirds
  - 叠加层：文本、图像、品牌、下三分之一字幕
- Data visualizations: charts, stats, animated numbers
  - 数据可视化：图表、统计数据、动态数字
- Motion graphics: transitions, explainer animations
  - 动态图形：转场、解释动画
- Composable scenes: reusable templates across videos
  - 可组合场景：跨视频的可重用模板
- Product demos: annotated screenshots, UI highlights
  - 产品演示：带注释的截图、UI 高亮

### Basic Remotion composition

### Basic Remotion composition
### 基本 Remotion 合成

```tsx
import { AbsoluteFill, Sequence, Video, useCurrentFrame } from "remotion";

export const VlogComposition: React.FC = () => {
  const frame = useCurrentFrame();

  return (
    <AbsoluteFill>
      {/* Main footage */}
      <Sequence from={0} durationInFrames={300}>
        <Video src="/segments/intro.mp4" />
      </Sequence>

      {/* Title overlay */}
      <Sequence from={30} durationInFrames={90}>
        <AbsoluteFill style={{
          justifyContent: "center",
          alignItems: "center",
        }}>
          <h1 style={{
            fontSize: 72,
            color: "white",
            textShadow: "2px 2px 8px rgba(0,0,0,0.8)",
          }}>
            The AI Editing Stack
          </h1>
        </AbsoluteFill>
      </Sequence>

      {/* Next segment */}
      <Sequence from={300} durationInFrames={450}>
        <Video src="/segments/demo.mp4" />
      </Sequence>
    </AbsoluteFill>
  );
};
```

### Render output

### Render output
### 渲染输出

```bash
npx remotion render src/index.ts VlogComposition output.mp4
```

See the [Remotion docs](https://www.remotion.dev/docs) for detailed patterns and API reference.

详细模式和 API 参考见 [Remotion 文档](https://www.remotion.dev/docs)。

## Layer 5: Generated Assets (ElevenLabs / fal.ai)

## Layer 5: Generated Assets (ElevenLabs / fal.ai)
## 层级 5：生成素材（ElevenLabs / fal.ai）

Generate only what you need. Do not generate the whole video.

只生成你需要的。不要生成整个视频。

### Voiceover with ElevenLabs

### Voiceover with ElevenLabs
### 使用 ElevenLabs 配音

```python
import os
import requests

resp = requests.post(
    f"https://api.elevenlabs.io/v1/text-to-speech/{voice_id}",
    headers={
        "xi-api-key": os.environ["ELEVENLABS_API_KEY"],
        "Content-Type": "application/json"
    },
    json={
        "text": "Your narration text here",
        "model_id": "eleven_turbo_v2_5",
        "voice_settings": {"stability": 0.5, "similarity_boost": 0.75}
    }
)
with open("voiceover.mp3", "wb") as f:
    f.write(resp.content)
```

### Music and SFX with fal.ai

### Music and SFX with fal.ai
### 使用 fal.ai 音乐和音效

Use the `fal-ai-media` skill for:
- Background music generation
  - 背景音乐生成
- Sound effects (ThinkSound model for video-to-audio)
  - 音效（ThinkSound 模型用于视频转音频）
- Transition sounds
  - 转场音效

### Generated visuals with fal.ai

### Generated visuals with fal.ai
### 使用 fal.ai 生成视觉素材

Use for insert shots, thumbnails, or b-roll that doesn't exist:
用于不存在的插入镜头、缩略图或 B-roll：

```
generate(app_id: "fal-ai/nano-banana-pro", input_data: {
  "prompt": "professional thumbnail for tech vlog, dark background, code on screen",
  "image_size": "landscape_16_9"
})
```

### VideoDB generative audio

### VideoDB generative audio
### VideoDB 生成音频

If VideoDB is configured:
如果 VideoDB 已配置：

```python
voiceover = coll.generate_voice(text="Narration here", voice="alloy")
music = coll.generate_music(prompt="lo-fi background for coding vlog", duration=120)
sfx = coll.generate_sound_effect(prompt="subtle whoosh transition")
```

## Layer 6: Final Polish (Descript / CapCut)

## Layer 6: Final Polish (Descript / CapCut)
## 层级 6：最终精修（Descript / CapCut）

The last layer is human. Use a traditional editor for:
最后一层是人工。使用传统编辑器进行：
- **Pacing**: adjust cuts that feel too fast or slow
  - **节奏**：调整感觉太快或太慢的剪切
- **Captions**: auto-generated, then manually cleaned
  - **字幕**：自动生成，然后手动清理
- **Color grading**: basic correction and mood
  - **调色**：基本校正和氛围
- **Final audio mix**: balance voice, music, and SFX levels
  - **最终音频混音**：平衡人声、音乐和音效电平
- **Export**: platform-specific formats and quality settings
  - **导出**：平台特定格式和质量设置

This is where taste lives. AI clears the repetitive work. You make the final calls.

这里是品味所在。AI 清除重复性工作。你做最终决定。

## Social Media Reframing

## Social Media Reframing
## 社交媒体重新构图

Different platforms need different aspect ratios:

不同平台需要不同的宽高比：

| Platform | Aspect Ratio | Resolution | 平台 | 宽高比 | 分辨率 |
|----------|-------------|------------|------|--------|------|
| YouTube | 16:9 | 1920x1080 | YouTube | 16:9 | 1920x1080 |
| TikTok / Reels | 9:16 | 1080x1920 | TikTok / Reels | 9:16 | 1080x1920 |
| Instagram Feed | 1:1 | 1080x1080 | Instagram Feed | 1:1 | 1080x1080 |
| X / Twitter | 16:9 or 1:1 | 1280x720 or 720x720 | X / Twitter | 16:9 或 1:1 | 1280x720 或 720x720 |

### Reframe with FFmpeg

### Reframe with FFmpeg
### 使用 FFmpeg 重新构图

```bash
# 16:9 to 9:16 (center crop)
ffmpeg -i input.mp4 -vf "crop=ih*9/16:ih,scale=1080:1920" vertical.mp4

# 16:9 to 1:1 (center crop)
ffmpeg -i input.mp4 -vf "crop=ih:ih,scale=1080:1080" square.mp4
```

### Reframe with VideoDB

### Reframe with VideoDB
### 使用 VideoDB 重新构图

```python
from videodb import ReframeMode

# Smart reframe (AI-guided subject tracking)
reframed = video.reframe(start=0, end=60, target="vertical", mode=ReframeMode.smart)
```

## Scene Detection and Auto-Cut

## Scene Detection and Auto-Cut
## 场景检测和自动剪切

### FFmpeg scene detection

### FFmpeg scene detection
### FFmpeg 场景检测

```bash
# Detect scene changes (threshold 0.3 = moderate sensitivity)
ffmpeg -i input.mp4 -vf "select='gt(scene,0.3)',showinfo" -vsync vfr -f null - 2>&1 | grep showinfo
```

### Silence detection for auto-cut

### Silence detection for auto-cut
### 静音检测用于自动剪切

```bash
# Find silent segments (useful for cutting dead air)
ffmpeg -i input.mp4 -af silencedetect=noise=-30dB:d=2 -f null - 2>&1 | grep silence
```

### Highlight extraction

### Highlight extraction
### 高光提取

Use Claude to analyze transcript + scene timestamps:
使用 Claude 分析转录本 + 场景时间戳：

```
"Given this transcript with timestamps and these scene change points,
identify the 5 most engaging 30-second clips for social media."
```

## What Each Tool Does Best

## What Each Tool Does Best
## 每个工具擅长什么

| Tool | Strength | Weakness | 工具 | 优势 | 劣势 |
|------|----------|----------|------|------|------|
| Claude / Codex | Organization, planning, code generation | Not the creative taste layer | Claude / Codex | 组织、规划、代码生成 | 不是创意品味层 |
| FFmpeg | Deterministic cuts, batch processing, format conversion | No visual editing UI | FFmpeg | 确定性剪切、批处理、格式转换 | 没有视觉编辑 UI |
| Remotion | Programmable overlays, composable scenes, reusable templates | Learning curve for non-devs | Remotion | 可编程叠加层、可组合场景、可重用模板 | 非开发者的学习曲线 |
| Screen Studio | Polished screen recordings immediately | Only screen capture | Screen Studio | 立即生成精致屏幕录制 | 仅屏幕录制 |
| ElevenLabs | Voice, narration, music, SFX | Not the center of the workflow | ElevenLabs | 人声、配音、音乐、音效 | 不是工作流中心 |
| Descript / CapCut | Final pacing, captions, polish | Manual, not automatable | Descript / CapCut | 最终节奏、字幕、精修 | 手动，不可自动化 |

## Key Principles

## Key Principles
## 关键原则

1. **Edit, don't generate.** This workflow is for cutting real footage, not creating from prompts.
   - **编辑，不要生成。** 此工作流用于剪切真实素材，而非从提示创建。
2. **Structure before style.** Get the story right in Layer 2 before touching anything visual.
   - **结构先于风格。** 在触及任何视觉元素之前，先在第 2 层把故事讲对。
3. **FFmpeg is the backbone.** Boring but critical. Where long footage becomes manageable.
   - **FFmpeg 是支柱。** 无聊但关键。长素材在这里变得可管理。
4. **Remotion for repeatability.** If you'll do it more than once, make it a Remotion component.
   - **Remotion 实现可重复性。** 如果你要做多次，把它做成 Remotion 组件。
5. **Generate selectively.** Only use AI generation for assets that don't exist, not for everything.
   - **选择性生成。** 仅对不存在的素材使用 AI 生成，而非一切。
6. **Taste is the last layer.** AI clears repetitive work. You make the final creative calls.
   - **品味是最后层。** AI 清除重复性工作。你做最终创意决定。

## Related Skills

## Related Skills
## 相关 Skills

- `fal-ai-media` — AI image, video, and audio generation
  - `fal-ai-media` — AI 图像、视频和音频生成
- `videodb` — Server-side video processing, indexing, and streaming
  - `videodb` — 服务器端视频处理、索引和流媒体
- `content-engine` — Platform-native content distribution
  - `content-engine` — 平台原生内容分发
