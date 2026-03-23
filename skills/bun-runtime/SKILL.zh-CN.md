---
name: bun-runtime
description: Bun as runtime, package manager, bundler, and test runner. When to choose Bun vs Node, migration notes, and Vercel support.
description zh-CN: Bun 作为运行时、包管理器、打包工具和测试运行器。介绍何时选择 Bun vs Node、迁移注意事项及 Vercel 支持。
origin: ECC
---

# Bun Runtime
# Bun 运行时

Bun is a fast all-in-one JavaScript runtime and toolkit: runtime, package manager, bundler, and test runner.
Bun 是一个快速的一体化 JavaScript 运行时和工具集：运行时、包管理器、打包工具和测试运行器。

## When to Use
## 何时使用

- **Prefer Bun** for: new JS/TS projects, scripts where install/run speed matters, Vercel deployments with Bun runtime, and when you want a single toolchain (run + install + test + build).
  - 优先选择 Bun：新 JS/TS 项目、需要快速安装/运行的脚本、使用 Bun 运行时的 Vercel 部署，以及需要统一工具链（运行 + 安装 + 测试 + 构建）的场景。
- **Prefer Node** for: maximum ecosystem compatibility, legacy tooling that assumes Node, or when a dependency has known Bun issues.
  - 优先选择 Node：需要最大生态系统兼容性、依赖假设使用 Node 的旧工具链，或某依赖存在已知 Bun 问题时。

Use when: adopting Bun, migrating from Node, writing or debugging Bun scripts/tests, or configuring Bun on Vercel or other platforms.
使用场景：采用 Bun、从 Node 迁移、编写或调试 Bun 脚本/测试，或在 Vercel 或其他平台上配置 Bun。

## How It Works
## 工作原理

- **Runtime**: Drop-in Node-compatible runtime (built on JavaScriptCore, implemented in Zig).
  - **运行时**：可直接替换的 Node 兼容运行时（基于 JavaScriptCore，使用 Zig 实现）。
- **Package manager**: `bun install` is significantly faster than npm/yarn. Lockfile is `bun.lock` (text) by default in current Bun; older versions used `bun.lockb` (binary).
  - **包管理器**：`bun install` 显著快于 npm/yarn。当前 Bun 默认使用 `bun.lock`（文本）作为锁文件；旧版本使用 `bun.lockb`（二进制）。
- **Bundler**: Built-in bundler and transpiler for apps and libraries.
  - **打包工具**：内置的应用和库打包工具及转译器。
- **Test runner**: Built-in `bun test` with Jest-like API.
  - **测试运行器**：内置的 `bun test`，提供类似 Jest 的 API。

**Migration from Node**: Replace `node script.js` with `bun run script.js` or `bun script.js`. Run `bun install` in place of `npm install`; most packages work. Use `bun run` for npm scripts; `bun x` for npx-style one-off runs. Node built-ins are supported; prefer Bun APIs where they exist for better performance.
**从 Node 迁移**：将 `node script.js` 替换为 `bun run script.js` 或 `bun script.js`。用 `bun install` 代替 `npm install`；大多数包都可以正常工作。用 `bun run` 执行 npm 脚本；用 `bun x` 执行类似 npx 的一次性运行。Node 内置模块受支持；在有 Bun API 的地方优先使用以获得更好性能。

**Vercel**: Set runtime to Bun in project settings. Build: `bun run build` or `bun build ./src/index.ts --outdir=dist`. Install: `bun install --frozen-lockfile` for reproducible deploys.
**Vercel**：在项目设置中将运行时设为 Bun。构建：`bun run build` 或 `bun build ./src/index.ts --outdir=dist`。安装：`bun install --frozen-lockfile` 以实现可重现的部署。

## Examples
## 示例

### Run and install
### 运行和安装

```bash
# Install dependencies (creates/updates bun.lock or bun.lockb)
# 安装依赖（创建/更新 bun.lock 或 bun.lockb）
bun install

# Run a script or file
# 运行脚本或文件
bun run dev
bun run src/index.ts
bun src/index.ts
```

### Scripts and env
### 脚本和环境变量

```bash
bun run --env-file=.env dev
FOO=bar bun run script.ts
```

### Testing
### 测试

```bash
bun test
bun test --watch
```

```typescript
// test/example.test.ts
import { expect, test } from "bun:test";

test("add", () => {
  expect(1 + 2).toBe(3);
});
```

### Runtime API
### 运行时 API

```typescript
const file = Bun.file("package.json");
const json = await file.json();

Bun.serve({
  port: 3000,
  fetch(req) {
    return new Response("Hello");
  },
});
```

## Best Practices
## 最佳实践

- Commit the lockfile (`bun.lock` or `bun.lockb`) for reproducible installs.
  - 提交锁文件（`bun.lock` 或 `bun.lockb`）以确保可重现的安装。
- Prefer `bun run` for scripts. For TypeScript, Bun runs `.ts` natively.
  - 优先使用 `bun run` 执行脚本。对于 TypeScript，Bun 原生运行 `.ts` 文件。
- Keep dependencies up to date; Bun and the ecosystem evolve quickly.
  - 保持依赖更新；Bun 和其生态系统发展很快。
