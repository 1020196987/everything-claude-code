---
name: liquid-glass-design
description: iOS 26 Liquid Glass design system — dynamic glass material with blur, reflection, and interactive morphing for SwiftUI, UIKit, and WidgetKit.
description zh-CN: iOS 26 Liquid Glass 设计系统 — 动态玻璃材质，带模糊、反射和交互式变形效果，适用于 SwiftUI、UIKit 和 WidgetKit。
---

# Liquid Glass Design System (iOS 26)

## Liquid Glass Design System (iOS 26)
## Liquid Glass 设计系统（iOS 26）

Patterns for implementing Apple's Liquid Glass — a dynamic material that blurs content behind it, reflects color and light from surrounding content, and reacts to touch and pointer interactions. Covers SwiftUI, UIKit, and WidgetKit integration.
实现 Apple Liquid Glass 的模式 — 一种动态材质，会模糊其后面的内容，反射周围内容的颜色和光线，并对触摸和指针交互做出反应。涵盖 SwiftUI、UIKit 和 WidgetKit 集成。

## When to Activate

## When to Activate
## 何时激活

- Building or updating apps for iOS 26+ with the new design language
  - 使用新的设计语言构建或更新 iOS 26+ 应用程序
- Implementing glass-style buttons, cards, toolbars, or containers
  - 实现玻璃风格按钮、卡片、工具栏或容器
- Creating morphing transitions between glass elements
  - 在玻璃元素之间创建变形过渡
- Applying Liquid Glass effects to widgets
  - 将 Liquid Glass 效果应用到小组件
- Migrating existing blur/material effects to the new Liquid Glass API
  - 将现有的模糊/材质效果迁移到新的 Liquid Glass API

## Core Pattern — SwiftUI

## Core Pattern — SwiftUI
## 核心模式 — SwiftUI

### Basic Glass Effect

### Basic Glass Effect
### 基础玻璃效果

The simplest way to add Liquid Glass to any view:
向任何视图添加 Liquid Glass 的最简单方法：

```swift
Text("Hello, World!")
    .font(.title)
    .padding()
    .glassEffect()  // Default: regular variant, capsule shape
                    // 默认：常规变体，胶囊形状
```

### Customizing Shape and Tint

### Customizing Shape and Tint
### 自定义形状和色调

```swift
Text("Hello, World!")
    .font(.title)
    .padding()
    .glassEffect(.regular.tint(.orange).interactive(), in: .rect(cornerRadius: 16.0))
```

Key customization options:
关键自定义选项：
- `.regular` — standard glass effect
  - `.regular` — 标准玻璃效果
- `.tint(Color)` — add color tint for prominence
  - `.tint(Color)` — 添加颜色色调以突出显示
- `.interactive()` — react to touch and pointer interactions
  - `.interactive()` — 对触摸和指针交互做出反应
- Shape: `.capsule` (default), `.rect(cornerRadius:)`, `.circle`
  - 形状：`.capsule`（默认）、`.rect(cornerRadius:)`、`.circle`

### Glass Button Styles

### Glass Button Styles
### 玻璃按钮样式

```swift
Button("Click Me") { /* action */ }
    .buttonStyle(.glass)

Button("Important") { /* action */ }
    .buttonStyle(.glassProminent)
```

### GlassEffectContainer for Multiple Elements

### GlassEffectContainer for Multiple Elements
### GlassEffectContainer（多个元素）

Always wrap multiple glass views in a container for performance and morphing:
始终将多个玻璃视图包装在容器中以提高性能和变形效果：

```swift
GlassEffectContainer(spacing: 40.0) {
    HStack(spacing: 40.0) {
        Image(systemName: "scribble.variable")
            .frame(width: 80.0, height: 80.0)
            .font(.system(size: 36))
            .glassEffect()

        Image(systemName: "eraser.fill")
            .frame(width: 80.0, height: 80.0)
            .font(.system(size: 36))
            .glassEffect()
    }
}
```

The `spacing` parameter controls merge distance — closer elements blend their glass shapes together.
`spacing` 参数控制合并距离 — 距离较近的元素会将它们的玻璃形状融合在一起。

### Uniting Glass Effects

### Uniting Glass Effects
### 联合玻璃效果

Combine multiple views into a single glass shape with `glassEffectUnion`:
使用 `glassEffectUnion` 将多个视图组合成单个玻璃形状：

```swift
@Namespace private var namespace

GlassEffectContainer(spacing: 20.0) {
    HStack(spacing: 20.0) {
        ForEach(symbolSet.indices, id: \.self) { item in
            Image(systemName: symbolSet[item])
                .frame(width: 80.0, height: 80.0)
                .glassEffect()
                .glassEffectUnion(id: item < 2 ? "group1" : "group2", namespace: namespace)
        }
    }
}
```

### Morphing Transitions

### Morphing Transitions
### 变形过渡

Create smooth morphing when glass elements appear/disappear:
在玻璃元素出现/消失时创建平滑变形：

```swift
@State private var isExpanded = false
@Namespace private var namespace

GlassEffectContainer(spacing: 40.0) {
    HStack(spacing: 40.0) {
        Image(systemName: "scribble.variable")
            .frame(width: 80.0, height: 80.0)
            .glassEffect()
            .glassEffectID("pencil", in: namespace)

        if isExpanded {
            Image(systemName: "eraser.fill")
                .frame(width: 80.0, height: 80.0)
                .glassEffect()
                .glassEffectID("eraser", in: namespace)
        }
    }
}

Button("Toggle") {
    withAnimation { isExpanded.toggle() }
}
.buttonStyle(.glass)
```

### Extending Horizontal Scrolling Under Sidebar

### Extending Horizontal Scrolling Under Sidebar
### 在侧边栏下扩展水平滚动

To allow horizontal scroll content to extend under a sidebar or inspector, ensure the `ScrollView` content reaches the leading/trailing edges of the container. The system automatically handles the under-sidebar scrolling behavior when the layout extends to the edges — no additional modifier is needed.
要允许水平滚动内容在侧边栏或检查器下延伸，确保 `ScrollView` 内容到达容器的起始/结束边缘。当布局延伸到边缘时，系统自动处理侧边栏下滚动行为 — 无需额外的修饰符。

## Core Pattern — UIKit

## Core Pattern — UIKit
## 核心模式 — UIKit

### Basic UIGlassEffect

### Basic UIGlassEffect
### 基础 UIGlassEffect

```swift
let glassEffect = UIGlassEffect()
glassEffect.tintColor = UIColor.systemBlue.withAlphaComponent(0.3)
glassEffect.isInteractive = true

let visualEffectView = UIVisualEffectView(effect: glassEffect)
visualEffectView.translatesAutoresizingMaskIntoConstraints = false
visualEffectView.layer.cornerRadius = 20
visualEffectView.clipsToBounds = true

view.addSubview(visualEffectView)
NSLayoutConstraint.activate([
    visualEffectView.centerXAnchor.constraint(equalTo: view.centerXAnchor),
    visualEffectView.centerYAnchor.constraint(equalTo: view.centerYAnchor),
    visualEffectView.widthAnchor.constraint(equalToConstant: 200),
    visualEffectView.heightAnchor.constraint(equalToConstant: 120)
])

// Add content to contentView
// 添加内容到 contentView
let label = UILabel()
label.text = "Liquid Glass"
label.translatesAutoresizingMaskIntoConstraints = false
visualEffectView.contentView.addSubview(label)
NSLayoutConstraint.activate([
    label.centerXAnchor.constraint(equalTo: visualEffectView.contentView.centerXAnchor),
    label.centerYAnchor.constraint(equalTo: visualEffectView.contentView.centerYAnchor)
])
```

### UIGlassContainerEffect for Multiple Elements

### UIGlassContainerEffect for Multiple Elements
### UIGlassContainerEffect（多个元素）

```swift
let containerEffect = UIGlassContainerEffect()
containerEffect.spacing = 40.0

let containerView = UIVisualEffectView(effect: containerEffect)

let firstGlass = UIVisualEffectView(effect: UIGlassEffect())
let secondGlass = UIVisualEffectView(effect: UIGlassEffect())

containerView.contentView.addSubview(firstGlass)
containerView.contentView.addSubview(secondGlass)
```

### Scroll Edge Effects

### Scroll Edge Effects
### 滚动边缘效果

```swift
scrollView.topEdgeEffect.style = .automatic
scrollView.bottomEdgeEffect.style = .hard
scrollView.leftEdgeEffect.isHidden = true
```

### Toolbar Glass Integration

### Toolbar Glass Integration
### 工具栏玻璃集成

```swift
let favoriteButton = UIBarButtonItem(image: UIImage(systemName: "heart"), style: .plain, target: self, action: #selector(favoriteAction))
favoriteButton.hidesSharedBackground = true  // Opt out of shared glass background
                                               // 选择退出共享玻璃背景
```

## Core Pattern — WidgetKit

## Core Pattern — WidgetKit
## 核心模式 — WidgetKit

### Rendering Mode Detection

### Rendering Mode Detection
### 渲染模式检测

```swift
struct MyWidgetView: View {
    @Environment(\.widgetRenderingMode) var renderingMode

    var body: some View {
        if renderingMode == .accented {
            // Tinted mode: white-tinted, themed glass background
            // 色调模式：白色色调、主题化玻璃背景
        } else {
            // Full color mode: standard appearance
            // 全色模式：标准外观
        }
    }
}
```

### Accent Groups for Visual Hierarchy

### Accent Groups for Visual Hierarchy
### 强调组（视觉层次）

```swift
HStack {
    VStack(alignment: .leading) {
        Text("Title")
            .widgetAccentable()  // Accent group
                                  // 强调组
        Text("Subtitle")
            // Primary group (default)
            // 主要组（默认）
    }
    Image(systemName: "star.fill")
        .widgetAccentable()  // Accent group
                              // 强调组
}
```

### Image Rendering in Accented Mode

### Image Rendering in Accented Mode
### 强调模式下的图像渲染

```swift
Image("myImage")
    .widgetAccentedRenderingMode(.monochrome)
```

### Container Background

### Container Background
### 容器背景

```swift
VStack { /* content */ }
    .containerBackground(for: .widget) {
        Color.blue.opacity(0.2)
    }
```

## Key Design Decisions

## Key Design Decisions
## 关键设计决策

| Decision | Rationale | Decision zh-CN | Rationale zh-CN |
|----------|-----------|---------------|----------------|
| GlassEffectContainer wrapping | Performance optimization, enables morphing between glass elements | GlassEffectContainer 包装 | 性能优化，支持玻璃元素之间的变形 |
| `spacing` parameter | Controls merge distance — fine-tune how close elements must be to blend | `spacing` 参数 | 控制合并距离 — 微调元素必须多近才能融合 |
| `@Namespace` + `glassEffectID` | Enables smooth morphing transitions on view hierarchy changes | `@Namespace` + `glassEffectID` | 启用视图层次结构变化时的平滑变形过渡 |
| `interactive()` modifier | Explicit opt-in for touch/pointer reactions — not all glass should respond | `interactive()` 修饰符 | 对触摸/指针反应进行显式选择加入 — 并非所有玻璃都应该响应 |
| UIGlassContainerEffect in UIKit | Same container pattern as SwiftUI for consistency | UIKit 中的 UIGlassContainerEffect | 与 SwiftUI 相同的容器模式以保持一致 |
| Accented rendering mode in widgets | System applies tinted glass when user selects tinted Home Screen | 小组件中的强调渲染模式 | 当用户选择色调主屏幕时，系统应用色调玻璃 |

## Best Practices

## Best Practices
## 最佳实践

- **Always use GlassEffectContainer** when applying glass to multiple sibling views — it enables morphing and improves rendering performance
  - **始终使用 GlassEffectContainer** 当向多个同级视图应用玻璃效果时 — 它支持变形并提高渲染性能
- **Apply `.glassEffect()` after** other appearance modifiers (frame, font, padding)
  - **在**其他外观修饰符（frame、font、padding）**之后应用 `.glassEffect()`**
- **Use `.interactive()`** only on elements that respond to user interaction (buttons, toggleable items)
  - **仅在**响应用户交互的元素（按钮、可切换项）**上使用 `.interactive()`**
- **Choose spacing carefully** in containers to control when glass effects merge
  - **仔细选择**容器中的间距以控制玻璃效果何时合并
- **Use `withAnimation`** when changing view hierarchies to enable smooth morphing transitions
  - **在**更改视图层次结构时使用 `withAnimation`** 以启用平滑的变形过渡
- **Test across appearances** — light mode, dark mode, and accented/tinted modes
  - **跨外观测试** — 浅色模式、深色模式和强调/色调模式
- **Ensure accessibility contrast** — text on glass must remain readable
  - **确保可访问性对比度** — 玻璃上的文本必须保持可读

## Anti-Patterns to Avoid

## Anti-Patterns to Avoid
## 应避免的反模式

- Using multiple standalone `.glassEffect()` views without a GlassEffectContainer
  - 在没有 GlassEffectContainer 的情况下使用多个独立的 `.glassEffect()` 视图
- Nesting too many glass effects — degrades performance and visual clarity
  - 嵌套过多玻璃效果 — 降低性能和视觉清晰度
- Applying glass to every view — reserve for interactive elements, toolbars, and cards
  - 向每个视图应用玻璃效果 — 仅用于交互元素、工具栏和卡片
- Forgetting `clipsToBounds = true` in UIKit when using corner radii
  - 在 UIKit 中使用圆角半径时忘记 `clipsToBounds = true`
- Ignoring accented rendering mode in widgets — breaks tinted Home Screen appearance
  - 忽略小组件中的强调渲染模式 — 破坏色调主屏幕外观
- Using opaque backgrounds behind glass — defeats the translucency effect
  - 在玻璃后面使用不透明背景 — 破坏半透明效果

## When to Use

## When to Use
## 何时使用

- Navigation bars, toolbars, and tab bars with the new iOS 26 design
  - 采用新的 iOS 26 设计的导航栏、工具栏和标签栏
- Floating action buttons and card-style containers
  - 浮动操作按钮和卡片式容器
- Interactive controls that need visual depth and touch feedback
  - 需要视觉深度和触摸反馈的交互控件
- Widgets that should integrate with the system's Liquid Glass appearance
  - 应与系统 Liquid Glass 外观集成的小组件
- Morphing transitions between related UI states
  - 相关 UI 状态之间的变形过渡
