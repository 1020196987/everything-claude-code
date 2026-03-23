# Style Presets Reference

## Style Presets Reference
## 样式预设参考

Curated visual styles for `frontend-slides`.
为 `frontend-slides` 精心设计的视觉风格。

Use this file for:
使用此文件来：

- the mandatory viewport-fitting CSS base
  - 必需的视口适配 CSS 基底
- preset selection and mood mapping
  - 预设选择和情绪映射
- CSS gotchas and validation rules
  - CSS 陷阱和验证规则

Abstract shapes only. Avoid illustrations unless the user explicitly asks for them.
仅使用抽象形状。除非用户明确要求，否则避免使用插图。

## Viewport Fit Is Non-Negotiable

## Viewport Fit Is Non-Negotiable
## 视口适配是必须的

Every slide must fully fit in one viewport.
每个幻灯片必须完全适应一个视口。

### Golden Rule

### Golden Rule
### 黄金法则

```text
Each slide = exactly one viewport height.
每张幻灯片 = 正好一个视口高度。
Too much content = split into more slides.
内容太多 = 拆分成更多幻灯片。
Never scroll inside a slide.
幻灯片内永远不要滚动。
```

### Density Limits

### Density Limits
### 密度限制

| Slide Type | Maximum Content |
|------------|-----------------|
| Title slide | 1 heading + 1 subtitle + optional tagline |
| 标题幻灯片 | 1 个标题 + 1 个副标题 + 可选标语 |
| Content slide | 1 heading + 4-6 bullets or 2 paragraphs |
| 内容幻灯片 | 1 个标题 + 4-6 个要点或 2 个段落 |
| Feature grid | 6 cards maximum |
| 功能网格 | 最多 6 张卡片 |
| Code slide | 8-10 lines maximum |
| 代码幻灯片 | 最多 8-10 行 |
| Quote slide | 1 quote + attribution |
| 引言幻灯片 | 1 条引言 + 出处 |
| Image slide | 1 image, ideally under 60vh |
| 图片幻灯片 | 1 张图片，最好低于 60vh |

## Mandatory Base CSS

## Mandatory Base CSS
## 必需的基线 CSS

Copy this block into every generated presentation and then theme on top of it.
将此块复制到每个生成的演示文稿中，然后在其上应用主题。

```css
/* ===========================================
   VIEWPORT FITTING: MANDATORY BASE STYLES
   =========================================== */

html, body {
    height: 100%;
    overflow-x: hidden;
}

html {
    scroll-snap-type: y mandatory;
    scroll-behavior: smooth;
}

.slide {
    width: 100vw;
    height: 100vh;
    height: 100dvh;
    overflow: hidden;
    scroll-snap-align: start;
    display: flex;
    flex-direction: column;
    position: relative;
}

.slide-content {
    flex: 1;
    display: flex;
    flex-direction: column;
    justify-content: center;
    max-height: 100%;
    overflow: hidden;
    padding: var(--slide-padding);
}

:root {
    --title-size: clamp(1.5rem, 5vw, 4rem);
    --h2-size: clamp(1.25rem, 3.5vw, 2.5rem);
    --h3-size: clamp(1rem, 2.5vw, 1.75rem);
    --body-size: clamp(0.75rem, 1.5vw, 1.125rem);
    --small-size: clamp(0.65rem, 1vw, 0.875rem);

    --slide-padding: clamp(1rem, 4vw, 4rem);
    --content-gap: clamp(0.5rem, 2vw, 2rem);
    --element-gap: clamp(0.25rem, 1vw, 1rem);
}

.card, .container, .content-box {
    max-width: min(90vw, 1000px);
    max-height: min(80vh, 700px);
}

.feature-list, .bullet-list {
    gap: clamp(0.4rem, 1vh, 1rem);
}

.feature-list li, .bullet-list li {
    font-size: var(--body-size);
    line-height: 1.4;
}

.grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(min(100%, 250px), 1fr));
    gap: clamp(0.5rem, 1.5vw, 1rem);
}

img, .image-container {
    max-width: 100%;
    max-height: min(50vh, 400px);
    object-fit: contain;
}

@media (max-height: 700px) {
    :root {
        --slide-padding: clamp(0.75rem, 3vw, 2rem);
        --content-gap: clamp(0.4rem, 1.5vw, 1rem);
        --title-size: clamp(1.25rem, 4.5vw, 2.5rem);
        --h2-size: clamp(1rem, 3vw, 1.75rem);
    }
}

@media (max-height: 600px) {
    :root {
        --slide-padding: clamp(0.5rem, 2.5vw, 1.5rem);
        --content-gap: clamp(0.3rem, 1vw, 0.75rem);
        --title-size: clamp(1.1rem, 4vw, 2rem);
        --body-size: clamp(0.7rem, 1.2vw, 0.95rem);
    }

    .nav-dots, .keyboard-hint, .decorative {
        display: none;
    }
}

@media (max-height: 500px) {
    :root {
        --slide-padding: clamp(0.4rem, 2vw, 1rem);
        --title-size: clamp(1rem, 3.5vw, 1.5rem);
        --h2-size: clamp(0.9rem, 2.5vw, 1.25rem);
        --body-size: clamp(0.65rem, 1vw, 0.85rem);
    }
}

@media (max-width: 600px) {
    :root {
        --title-size: clamp(1.25rem, 7vw, 2.5rem);
    }

    .grid {
        grid-template-columns: 1fr;
    }
}

@media (prefers-reduced-motion: reduce) {
    *, *::before, *::after {
        animation-duration: 0.01ms !important;
        transition-duration: 0.2s !important;
    }

    html {
        scroll-behavior: auto;
    }
}
```

## Viewport Checklist

## Viewport Checklist
## 视口检查清单

- every `.slide` has `height: 100vh`, `height: 100dvh`, and `overflow: hidden`
  - 每个 `.slide` 都有 `height: 100vh`、`height: 100dvh` 和 `overflow: hidden`
- all typography uses `clamp()`
  - 所有字体大小使用 `clamp()`
- all spacing uses `clamp()` or viewport units
  - 所有间距使用 `clamp()` 或视口单位
- images have `max-height` constraints
  - 图片有 `max-height` 约束
- grids adapt with `auto-fit` + `minmax()`
  - 网格使用 `auto-fit` + `minmax()` 自适应
- short-height breakpoints exist at `700px`, `600px`, and `500px`
  - 短高度断点存在于 `700px`、`600px` 和 `500px`
- if anything feels cramped, split the slide
  - 如果任何内容感觉拥挤，就拆分幻灯片

## Mood to Preset Mapping

## Mood to Preset Mapping
## 情绪到预设的映射

| Mood | Good Presets |
|------|--------------|
| Impressed / Confident | Bold Signal, Electric Studio, Dark Botanical |
| 印象深刻 / 自信 | Bold Signal, Electric Studio, Dark Botanical |
| Excited / Energized | Creative Voltage, Neon Cyber, Split Pastel |
| 兴奋 / 充满活力 | Creative Voltage, Neon Cyber, Split Pastel |
| Calm / Focused | Notebook Tabs, Paper & Ink, Swiss Modern |
| 冷静 / 专注 | Notebook Tabs, Paper & Ink, Swiss Modern |
| Inspired / Moved | Dark Botanical, Vintage Editorial, Pastel Geometry |
| 受启发 / 感动 | Dark Botanical, Vintage Editorial, Pastel Geometry |

## Preset Catalog

## Preset Catalog
## 预设目录

### 1. Bold Signal

### 1. Bold Signal
### 1. 醒目信号

- Vibe: confident, high-impact, keynote-ready
  - 氛围：自信、高冲击力、主题演讲风格
- Best for: pitch decks, launches, statements
  - 适用：宣传幻灯片、产品发布、声明
- Fonts: Archivo Black + Space Grotesk
  - 字体：Archivo Black + Space Grotesk
- Palette: charcoal base, hot orange focal card, crisp white text
  - 调色板：炭灰色底色、热橙色焦点卡片、清晰白色文字
- Signature: oversized section numbers, high-contrast card on dark field
  - 特色：超大节编号、深色背景上的高对比度卡片

### 2. Electric Studio

### 2. Electric Studio
### 2. 电动工作室

- Vibe: clean, bold, agency-polished
  - 氛围：简洁、大胆、代理风格
- Best for: client presentations, strategic reviews
  - 适用：客户演示、战略评审
- Fonts: Manrope only
  - 字体：仅 Manrope
- Palette: black, white, saturated cobalt accent
  - 调色板：黑色、白色、饱和钴蓝色强调
- Signature: two-panel split and sharp editorial alignment
  - 特色：双栏分割和锐利的编辑对齐

### 3. Creative Voltage

### 3. Creative Voltage
### 3. 创意电压

- Vibe: energetic, retro-modern, playful confidence
  - 氛围：充满活力、复古现代、玩味自信
- Best for: creative studios, brand work, product storytelling
  - 适用：创意工作室、品牌工作、产品叙事
- Fonts: Syne + Space Mono
  - 字体：Syne + Space Mono
- Palette: electric blue, neon yellow, deep navy
  - 调色板：电蓝色、霓虹黄色、深海军蓝
- Signature: halftone textures, badges, punchy contrast
  - 特色：半色调纹理、徽章、有力的对比

### 4. Dark Botanical

### 4. Dark Botanical
### 4. 暗调植物

- Vibe: elegant, premium, atmospheric
  - 氛围：优雅、高端、有氛围感
- Best for: luxury brands, thoughtful narratives, premium product decks
  - 适用：奢侈品牌、深思熟虑的叙事、高端产品幻灯片
- Fonts: Cormorant + IBM Plex Sans
  - 字体：Cormorant + IBM Plex Sans
- Palette: near-black, warm ivory, blush, gold, terracotta
  - 调色板：近黑色、暖象牙色、腮红金色、金色、赤陶色
- Signature: blurred abstract circles, fine rules, restrained motion
  - 特色：模糊的抽象圆形、精细线条、克制的动效

### 5. Notebook Tabs

### 5. Notebook Tabs
### 5. 笔记本标签

- Vibe: editorial, organized, tactile
  - 氛围：编辑风格、有组织、质感
- Best for: reports, reviews, structured storytelling
  - 适用：报告、评审、结构化叙事
- Fonts: Bodoni Moda + DM Sans
  - 字体：Bodoni Moda + DM Sans
- Palette: cream paper on charcoal with pastel tabs
  - 调色板：炭灰色背景上的奶油色纸张配粉彩标签
- Signature: paper sheet, colored side tabs, binder details
  - 特色：纸张效果、彩色侧边标签、活页细节

### 6. Pastel Geometry

### 6. Pastel Geometry
### 6. 粉彩几何

- Vibe: approachable, modern, friendly
  - 氛围：平易近人、现代、友好
- Best for: product overviews, onboarding, lighter brand decks
  - 适用：产品概述、入门引导、轻量品牌幻灯片
- Fonts: Plus Jakarta Sans only
  - 字体：仅 Plus Jakarta Sans
- Palette: pale blue field, cream card, soft pink/mint/lavender accents
  - 调色板：淡蓝色区域、奶油色卡片、柔和粉色/薄荷色/薰衣草色强调
- Signature: vertical pills, rounded cards, soft shadows
  - 特色：垂直胶囊、圆角卡片、柔和阴影

### 7. Split Pastel

### 7. Split Pastel
### 7. 分割粉彩

- Vibe: playful, modern, creative
  - 氛围：活泼、现代、创意
- Best for: agency intros, workshops, portfolios
  - 适用：代理介绍、工作坊、作品集
- Fonts: Outfit only
  - 字体：仅 Outfit
- Palette: peach + lavender split with mint badges
  - 调色板：桃色 + 薰衣草色分割配薄荷色徽章
- Signature: split backdrop, rounded tags, light grid overlays
  - 特色：分割背景、圆角标签、浅色网格叠加

### 8. Vintage Editorial

### 8. Vintage Editorial
### 8. 复古编辑

- Vibe: witty, personality-driven, magazine-inspired
  - 氛围：诙谐、个性驱动、杂志风格
- Best for: personal brands, opinionated talks, storytelling
  - 适用：个人品牌、有观点的演讲、叙事
- Fonts: Fraunces + Work Sans
  - 字体：Fraunces + Work Sans
- Palette: cream, charcoal, dusty warm accents
  - 调色板：奶油色、炭灰色、暖尘色调强调
- Signature: geometric accents, bordered callouts, punchy serif headlines
  - 特色：几何装饰、带边框的标注、有力的衬线标题

### 9. Neon Cyber

### 9. Neon Cyber
### 9. 霓虹赛博

- Vibe: futuristic, techy, kinetic
  - 氛围：未来感、科技感、动态感
- Best for: AI, infra, dev tools, future-of-X talks
  - 适用：AI、基础设施、开发工具、未来 X 演讲
- Fonts: Clash Display + Satoshi
  - 字体：Clash Display + Satoshi
- Palette: midnight navy, cyan, magenta
  - 调色板：午夜海军蓝、青色、品红色
- Signature: glow, particles, grids, data-radar energy
  - 特色：发光、粒子、网格、数据雷达能量

### 10. Terminal Green

### 10. Terminal Green
### 10. 终端绿

- Vibe: developer-focused, hacker-clean
  - 氛围：开发者聚焦、黑客风格
- Best for: APIs, CLI tools, engineering demos
  - 适用：API、CLI 工具、工程演示
- Fonts: JetBrains Mono only
  - 字体：仅 JetBrains Mono
- Palette: GitHub dark + terminal green
  - 调色板：GitHub 深色 + 终端绿色
- Signature: scan lines, command-line framing, precise monospace rhythm
  - 特色：扫描线、命令行边框、精确等宽字体节奏

### 11. Swiss Modern

### 11. Swiss Modern
### 11. 瑞士现代

- Vibe: minimal, precise, data-forward
  - 氛围：极简、精确、数据导向
- Best for: corporate, product strategy, analytics
  - 适用：企业、产品策略、分析
- Fonts: Archivo + Nunito
  - 字体：Archivo + Nunito
- Palette: white, black, signal red
  - 调色板：白色、黑色、信号红
- Signature: visible grids, asymmetry, geometric discipline
  - 特色：可见网格、不对称、几何纪律

### 12. Paper & Ink

### 12. Paper & Ink
### 12. 纸与墨

- Vibe: literary, thoughtful, story-driven
  - 氛围：文学、沉思、故事驱动
- Best for: essays, keynote narratives, manifesto decks
  - 适用：论文、主题演讲叙事、宣言幻灯片
- Fonts: Cormorant Garamond + Source Serif 4
  - 字体：Cormorant Garamond + Source Serif 4
- Palette: warm cream, charcoal, crimson accent
  - 调色板：暖奶油色、炭灰色、深红色强调
- Signature: pull quotes, drop caps, elegant rules
  - 特色：引用块、首字母放大、优雅分隔线

## Direct Selection Prompts

## Direct Selection Prompts
## 直接选择提示

If the user already knows the style they want, let them pick directly from the preset names above instead of forcing preview generation.
如果用户已经知道自己想要的风格，让他们直接从上面的预设名称中选择，而不是强制生成预览。

## Animation Feel Mapping

## Animation Feel Mapping
## 动画感觉映射

| Feeling | Motion Direction |
|---------|------------------|
| Dramatic / Cinematic | slow fades, parallax, large scale-ins |
| 戏剧性 / 电影感 | 慢淡入淡出、视差、大尺度放大 |
| Techy / Futuristic | glow, particles, grid motion, scramble text |
| 科技感 / 未来感 | 发光、粒子、网格动效、文字乱序 |
| Playful / Friendly | springy easing, rounded shapes, floating motion |
| 活泼 / 友好 | 弹性缓动、圆角形状、漂浮动效 |
| Professional / Corporate | subtle 200-300ms transitions, clean slides |
| 专业 / 企业 | 微妙的 200-300ms 过渡、简洁幻灯片 |
| Calm / Minimal | very restrained movement, whitespace-first |
| 冷静 / 极简 | 非常克制的动效、留白优先 |
| Editorial / Magazine | strong hierarchy, staggered text and image interplay |
| 编辑 / 杂志 | 强烈的层次感、错落的文字和图片互动 |

## CSS Gotcha: Negating Functions

## CSS Gotcha: Negating Functions
## CSS 陷阱：否定函数

Never write these:
永远不要写这些：

```css
right: -clamp(28px, 3.5vw, 44px);
margin-left: -min(10vw, 100px);
```

Browsers ignore them silently.
浏览器会静默忽略它们。

Always write this instead:
始终改为这样写：

```css
right: calc(-1 * clamp(28px, 3.5vw, 44px));
margin-left: calc(-1 * min(10vw, 100px));
```

## Validation Sizes

## Validation Sizes
## 验证尺寸

Test at minimum:
至少测试：

- Desktop: `1920x1080`, `1440x900`, `1280x720`
  - 桌面： `1920x1080`、`1440x900`、`1280x720`
- Tablet: `1024x768`, `768x1024`
  - 平板： `1024x768`、`768x1024`
- Mobile: `375x667`, `414x896`
  - 移动端： `375x667`、`414x896`
- Landscape phone: `667x375`, `896x414`
  - 横屏手机： `667x375`、`896x414`

## Anti-Patterns

## Anti-Patterns
## 反模式

Do not use:
不要使用：

- purple-on-white startup templates
  - 白底紫字的创业模板
- Inter / Roboto / Arial as the visual voice unless the user explicitly wants utilitarian neutrality
  - 使用 Inter / Roboto / Arial 作为视觉语言，除非用户明确要求功利性的中立
- bullet walls, tiny type, or code blocks that require scrolling
  - 大量要点、小字体或需要滚动的代码块
- decorative illustrations when abstract geometry would do the job better
  - 当抽象几何能更好地完成任务时使用装饰性插图
