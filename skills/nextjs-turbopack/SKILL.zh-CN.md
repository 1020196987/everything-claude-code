---
name: nextjs-turbopack
description: Next.js 16+ and Turbopack — incremental bundling, FS caching, dev speed, and when to use Turbopack vs webpack.
description zh-CN: Next.js 16+ 与 Turbopack — 增量打包、文件系统缓存、开发速度，以及何时使用 Turbopack 与 webpack 的对比。
origin: ECC
---

# Next.js and Turbopack

## Next.js and Turbopack
## Next.js 与 Turbopack

Next.js 16+ uses Turbopack by default for local development: an incremental bundler written in Rust that significantly speeds up dev startup and hot updates.
Next.js 16+ 在本地开发中默认使用 Turbopack：一个用 Rust 编写的增量打包工具，可显著加快开发启动速度和热更新。

## When to Use

## When to Use
## 何时使用

- **Turbopack (default dev)**: Use for day-to-day development. Faster cold start and HMR, especially in large apps.
- **Turbopack（默认开发）**：用于日常开发。冷启动和 HMR 更快，尤其是在大型应用中。
- **Webpack (legacy dev)**: Use only if you hit a Turbopack bug or rely on a webpack-only plugin in dev. Disable with `--webpack` (or `--no-turbopack` depending on your Next.js version; check the docs for your release).
- **Webpack（旧版开发）**：仅在遇到 Turbopack bug 或依赖 webpack 专用插件时使用。使用 `--webpack` 禁用（根据 Next.js 版本，也可能需要 `--no-turbopack`；请查看对应版本的文档）。
- **Production**: Production build behavior (`next build`) may use Turbopack or webpack depending on Next.js version; check the official Next.js docs for your version.
- **生产环境**：生产构建行为（`next build`）可能使用 Turbopack 或 webpack，取决于 Next.js 版本；请查看对应版本的官方 Next.js 文档。

Use when: developing or debugging Next.js 16+ apps, diagnosing slow dev startup or HMR, or optimizing production bundles.
使用场景：开发或调试 Next.js 16+ 应用、诊断开发启动或 HMR 缓慢问题，或优化生产包。

## How It Works

## How It Works
## 工作原理

- **Turbopack**: Incremental bundler for Next.js dev. Uses file-system caching so restarts are much faster (e.g. 5–14x on large projects).
- **Turbopack**：Next.js 开发的增量打包工具。使用文件系统缓存，重启速度大幅提升（例如大型项目快 5–14 倍）。
- **Default in dev**: From Next.js 16, `next dev` runs with Turbopack unless disabled.
- **开发默认**：从 Next.js 16 起，`next dev` 默认使用 Turbopack 运行（除非禁用）。
- **File-system caching**: Restarts reuse previous work; cache is typically under `.next`; no extra config needed for basic use.
- **文件系统缓存**：重启时复用之前的工作；缓存通常位于 `.next` 目录下；基本使用无需额外配置。
- **Bundle Analyzer (Next.js 16.1+)**: Experimental Bundle Analyzer to inspect output and find heavy dependencies; enable via config or experimental flag (see Next.js docs for your version).
- **Bundle Analyzer（Next.js 16.1+）**：实验性 Bundle Analyzer，用于检查输出和发现重依赖；通过配置或实验性标志启用（请查看对应版本的 Next.js 文档）。

## Examples

## Examples
## 示例

### Commands

### Commands
### 命令

```bash
next dev
next build
next start
```

### Usage

### Usage
### 使用方法

Run `next dev` for local development with Turbopack. Use the Bundle Analyzer (see Next.js docs) to optimize code-splitting and trim large dependencies. Prefer App Router and server components where possible.
运行 `next dev` 以使用 Turbopack 进行本地开发。使用 Bundle Analyzer（见 Next.js 文档）优化代码分割和削减大型依赖。尽可能优先使用 App Router 和服务端组件。

## Best Practices

## Best Practices
## 最佳实践

- Stay on a recent Next.js 16.x for stable Turbopack and caching behavior.
- 保持在较新的 Next.js 16.x 版本上，以获得稳定的 Turbopack 和缓存行为。
- If dev is slow, ensure you're on Turbopack (default) and that the cache isn't being cleared unnecessarily.
- 如果开发速度慢，请确保使用的是 Turbopack（默认），且缓存没有被不必要地清除。
- For production bundle size issues, use the official Next.js bundle analysis tooling for your version.
- 遇到生产包大小问题时，使用对应版本的官方 Next.js 包分析工具。
