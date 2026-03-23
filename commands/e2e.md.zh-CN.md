---
description: Generate and run end-to-end tests with Playwright. Creates test journeys, runs tests, captures screenshots/videos/traces, and uploads artifacts.
description-zh: 用 Playwright 生成和运行端到端测试。创建测试流程，运行测试，捕获截图/视频/跟踪，并上传产物。
---

# E2E Command
# E2E 命令

This command invokes the **e2e-runner** agent to generate, maintain, and execute end-to-end tests using Playwright.
此命令调用 **e2e-runner** agent 使用 Playwright 生成、维护和执行端到端测试。

## What This Command Does
## 此命令做什么

1. **Generate Test Journeys** - Create Playwright tests for user flows
   **生成测试流程** - 为用户流程创建 Playwright 测试
2. **Run E2E Tests** - Execute tests across browsers
   **运行 E2E 测试** - 跨浏览器执行测试
3. **Capture Artifacts** - Screenshots, videos, traces on failures
   **捕获产物** - 失败时的截图、视频、跟踪
4. **Upload Results** - HTML reports and JUnit XML
   **上传结果** - HTML 报告和 JUnit XML
5. **Identify Flaky Tests** - Quarantine unstable tests
   **识别不稳定测试** - 隔离不稳定测试

## When to Use
## 使用场景

Use `/e2e` when:
- Testing critical user journeys (login, trading, payments)
- Verifying multi-step flows work end-to-end
- Testing UI interactions and navigation
- Validating integration between frontend and backend
- Preparing for production deployment

使用 `/e2e` 当：
- 测试关键用户流程（登录、交易、支付）
- 验证多步骤流程端到端工作
- 测试 UI 交互和导航
- 验证前端和后端集成
- 准备生产部署

## Important Notes
## 重要注意事项

**CRITICAL for PMX:**
- E2E tests involving real money MUST run on testnet/staging only
  涉及真钱的 E2E 测试必须在 testnet/staging 上运行
- Never run trading tests against production
  永远不要对生产环境运行交易测试
- Set `test.skip(process.env.NODE_ENV === 'production')` for financial tests
  对金融测试设置 `test.skip(process.env.NODE_ENV === 'production')`
- Use test wallets with small test funds only
  仅使用小额测试资金的测试钱包
