---
name: frontend-slides
description: Create stunning, animation-rich HTML presentations from scratch or by converting PowerPoint files. Use when the user wants to build a presentation, convert a PPT/PPTX to web, or create slides for a talk/pitch. Helps non-designers discover their aesthetic through visual exploration rather than abstract choices.
description zh-CN: 从头开始创建令人惊叹的、动画丰富的HTML演示文稿，或通过转换PowerPoint文件。当用户想要构建演示文稿、将PPT/PPTX转换为网页或为演讲/推介创建幻灯片时使用。帮助非设计师通过视觉探索而非抽象选择来发现自己的美学风格。
origin: ECC
---

# Frontend Slides

## Frontend Slides
## 前端幻灯片

Create zero-dependency, animation-rich HTML presentations that run entirely in the browser.
创建零依赖、完全在浏览器中运行的动画丰富的HTML演示文稿。

Inspired by the visual exploration approach showcased in work by zarazhangrui (credit: @zarazhangrui).
灵感来自zarazhangrui作品中展示的视觉探索方法（鸣谢@zarazhangrui）。

## When to Activate

## When to Activate
## 何时激活

- Creating a talk deck, pitch deck, workshop deck, or internal presentation
  创建演讲稿、推介稿、工作坊稿或内部演示
- Converting `.ppt` or `.pptx` slides into an HTML presentation
  将`.ppt`或`.pptx`幻灯片转换为HTML演示文稿
- Improving an existing HTML presentation's layout, motion, or typography
  改进现有HTML演示文稿的布局、动画或排版
- Exploring presentation styles with a user who does not know their design preference yet
  与尚不了解设计偏好的用户一起探索演示风格

## Non-Negotiables

## Non-Negotiables
## 不可商量的事项

1. **Zero dependencies**: default to one self-contained HTML file with inline CSS and JS.
   **零依赖**：默认为一个自包含的HTML文件，内联CSS和JS。
2. **Viewport fit is mandatory**: every slide must fit inside one viewport with no internal scrolling.
   **视口适配是强制性的**：每张幻灯片必须适配一个视口，无内部滚动。
3. **Show, don't tell**: use visual previews instead of abstract style questionnaires.
   **展示而非说明**：使用视觉预览而非抽象的风格问卷。
4. **Distinctive design**: avoid generic purple-gradient, Inter-on-white, template-looking decks.
   **独特设计**：避免通用的紫色渐变、白色Inter字体、模板化的演示文稿。
5. **Production quality**: keep code commented, accessible, responsive, and performant.
   **生产质量**：保持代码有注释、可访问、响应式和高性能。

Before generating, read `STYLE_PRESETS.md` for the viewport-safe CSS base, density limits, preset catalog, and CSS gotchas.
在生成之前，请阅读`STYLE_PRESETS.md`以了解视口安全的CSS基础、密度限制、预设目录和CSS陷阱。

## Workflow

## Workflow
## 工作流程

### 1. Detect Mode

### 1. Detect Mode
### 1. 检测模式

Choose one path:
选择一条路径：

- **New presentation**: user has a topic, notes, or full draft
  **新演示文稿**：用户有主题、笔记或完整草稿
- **PPT conversion**: user has `.ppt` or `.pptx`
  **PPT转换**：用户有`.ppt`或`.pptx`
- **Enhancement**: user already has HTML slides and wants improvements
  **增强**：用户已有HTML幻灯片并希望改进

### 2. Discover Content

### 2. Discover Content
### 2. 发现内容

Ask only the minimum needed:
只询问最少需要的内容：

- purpose: pitch, teaching, conference talk, internal update
  目的：推介、教学、会议演讲、内部更新
- length: short (5-10), medium (10-20), long (20+)
  长度：短（5-10）、中（10-20）、长（20+）
- content state: finished copy, rough notes, topic only
  内容状态：完成的文稿、粗略笔记、仅主题

If the user has content, ask them to paste it before styling.
如果用户有内容，请在样式化之前让他们粘贴。

### 3. Discover Style

### 3. Discover Style
### 3. 发现风格

Default to visual exploration.
默认为视觉探索。

If the user already knows the desired preset, skip previews and use it directly.
如果用户已经知道想要的预设，跳过预览直接使用。

Otherwise:
否则：

1. Ask what feeling the deck should create: impressed, energized, focused, inspired.
   询问演示文稿应该创造什么感觉：印象深刻、充满活力、专注、受到启发。
2. Generate **3 single-slide preview files** in `.ecc-design/slide-previews/`.
   在`.ecc-design/slide-previews/`中生成**3个单页预览文件**。
3. Each preview must be self-contained, show typography/color/motion clearly, and stay under roughly 100 lines of slide content.
   每个预览必须是自包含的，清晰展示排版/颜色/动画，并保持在约100行幻灯片内容以内。
4. Ask the user which preview to keep or what elements to mix.
   询问用户保留哪个预览或混合哪些元素。

Use the preset guide in `STYLE_PRESETS.md` when mapping mood to style.
在将情绪映射到风格时使用`STYLE_PRESETS.md`中的预设指南。

### 4. Build the Presentation

### 4. Build the Presentation
### 4. 构建演示文稿

Output either:
输出以下任一：

- `presentation.html`
- `[presentation-name].html`

Use an `assets/` folder only when the deck contains extracted or user-supplied images.
仅当演示文稿包含提取的或用户提供的图像时才使用`assets/`文件夹。

Required structure:
必需结构：

- semantic slide sections
  语义化的幻灯片区域
- a viewport-safe CSS base from `STYLE_PRESETS.md`
  来自`STYLE_PRESETS.md`的视口安全CSS基础
- CSS custom properties for theme values
  CSS自定义属性用于主题值
- a presentation controller class for keyboard, wheel, and touch navigation
  用于键盘、滚轮和触摸导航的演示控制器类
- Intersection Observer for reveal animations
  Intersection Observer用于显示动画
- reduced-motion support
  支持减少动画

### 5. Enforce Viewport Fit

### 5. Enforce Viewport Fit
### 5. 强制视口适配

Treat this as a hard gate.
将其视为硬性关卡。

Rules:
规则：

- every `.slide` must use `height: 100vh; height: 100dvh; overflow: hidden;`
  每个`.slide`必须使用`height: 100vh; height: 100dvh; overflow: hidden;`
- all type and spacing must scale with `clamp()`
  所有字体和间距必须用`clamp()`缩放
- when content does not fit, split into multiple slides
  当内容不适合时，分成多张幻灯片
- never solve overflow by shrinking text below readable sizes
  永远不要通过将文本缩小到可读大小以下来解决溢出
- never allow scrollbars inside a slide
  永远不要在幻灯片内允许滚动条

Use the density limits and mandatory CSS block in `STYLE_PRESETS.md`.
使用`STYLE_PRESETS.md`中的密度限制和强制性CSS块。

### 6. Validate

### 6. Validate
### 6. 验证

Check the finished deck at these sizes:
在这些尺寸下检查完成的演示文稿：

- 1920x1080
- 1280x720
- 768x1024
- 375x667
- 667x375

If browser automation is available, use it to verify no slide overflows and that keyboard navigation works.
如果浏览器自动化可用，使用它来验证没有幻灯片溢出且键盘导航正常工作。

### 7. Deliver

### 7. Deliver
### 7. 交付

At handoff:
交付时：

- delete temporary preview files unless the user wants to keep them
  删除临时预览文件，除非用户想保留它们
- open the deck with the platform-appropriate opener when useful
  在有用时用平台适当的打开程序打开演示文稿
- summarize file path, preset used, slide count, and easy theme customization points
  总结文件路径、使用的预设、幻灯片数量和简单的主题自定义点

Use the correct opener for the current OS:
为当前操作系统使用正确的打开程序：

- macOS: `open file.html`
- Linux: `xdg-open file.html`
- Windows: `start "" file.html`

## PPT / PPTX Conversion

## PPT / PPTX Conversion
## PPT / PPTX转换

For PowerPoint conversion:
关于PowerPoint转换：

1. Prefer `python3` with `python-pptx` to extract text, images, and notes.
   优先使用`python3`和`python-pptx`提取文本、图像和备注。
2. If `python-pptx` is unavailable, ask whether to install it or fall back to a manual/export-based workflow.
   如果`python-pptx`不可用，询问是否安装或回退到手动/导出工作流程。
3. Preserve slide order, speaker notes, and extracted assets.
   保留幻灯片顺序、演讲者备注和提取的资源。
4. After extraction, run the same style-selection workflow as a new presentation.
   提取后，运行与新演示文稿相同的风格选择工作流程。

Keep conversion cross-platform. Do not rely on macOS-only tools when Python can do the job.
保持转换跨平台。当Python可以完成工作时，不要依赖仅macOS的工具。

## Implementation Requirements

## Implementation Requirements
## 实现要求

### HTML / CSS

- Use inline CSS and JS unless the user explicitly wants a multi-file project.
  使用内联CSS和JS，除非用户明确要求多文件项目。
- Fonts may come from Google Fonts or Fontshare.
  字体可以来自Google Fonts或Fontshare。
- Prefer atmospheric backgrounds, strong type hierarchy, and a clear visual direction.
  偏好有氛围的背景、强烈的字体层次和清晰的视觉方向。
- Use abstract shapes, gradients, grids, noise, and geometry rather than illustrations.
  使用抽象形状、渐变、网格、噪点和几何图形，而非插图。

### JavaScript

Include:
包含：

- keyboard navigation
  键盘导航
- touch / swipe navigation
  触摸/滑动导航
- mouse wheel navigation
  鼠标滚轮导航
- progress indicator or slide index
  进度指示器或幻灯片索引
- reveal-on-enter animation triggers
  进入时显示动画触发器

### Accessibility

- use semantic structure (`main`, `section`, `nav`)
  使用语义结构（`main`、`section`、`nav`）
- keep contrast readable
  保持对比度可读
- support keyboard-only navigation
  支持纯键盘导航
- respect `prefers-reduced-motion`
  尊重`prefers-reduced-motion`

## Content Density Limits

## Content Density Limits
## 内容密度限制

Use these maxima unless the user explicitly asks for denser slides and readability still holds:
除非用户明确要求更密集的幻灯片且可读性仍然成立，否则使用这些最大值：

| Slide type | Limit |
|------------|-------|
| Title | 1 heading + 1 subtitle + optional tagline |
| Content | 1 heading + 4-6 bullets or 2 short paragraphs |
| Feature grid | 6 cards max |
| Code | 8-10 lines max |
| Quote | 1 quote + attribution |
| Image | 1 image constrained by viewport |

## Anti-Patterns

## Anti-Patterns
## 反模式

- generic startup gradients with no visual identity
  没有视觉识别度的通用创业公司渐变
- system-font decks unless intentionally editorial
  除非有意编辑，否则使用系统字体的演示文稿
- long bullet walls
  冗长的要点墙
- code blocks that need scrolling
  需要滚动的代码块
- fixed-height content boxes that break on short screens
  在短屏幕上会破坏的固定高度内容框
- invalid negated CSS functions like `-clamp(...)`
  无效的否定CSS函数如`-clamp(...)`

## Related ECC Skills

## Related ECC Skills
## 相关ECC技能

- `frontend-patterns` for component and interaction patterns around the deck
  `frontend-patterns`用于演示文稿周围的组件和交互模式
- `liquid-glass-design` when a presentation intentionally borrows Apple glass aesthetics
  `liquid-glass-design`当演示文稿有意借用Apple玻璃美学时
- `e2e-testing` if you need automated browser verification for the final deck
  `e2e-testing`如果您需要对最终演示文稿进行自动化浏览器验证

## Deliverable Checklist

## Deliverable Checklist
## 交付清单

- presentation runs from a local file in a browser
  演示文稿在浏览器中从本地文件运行
- every slide fits the viewport without scrolling
  每张幻灯片适配视口，无需滚动
- style is distinctive and intentional
  风格独特且有意
- animation is meaningful, not noisy
  动画有意义，不杂乱
- reduced motion is respected
  尊重减少动画
- file paths and customization points are explained at handoff
  交付时解释文件路径和自定义点
