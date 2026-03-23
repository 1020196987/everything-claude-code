# The Hidden Danger of OpenClaw
# OpenClaw 的隐藏危险

![Header: The Hidden Danger of OpenClaw — Security Lessons from the Agent Frontier](./assets/images/openclaw/01-header.png)

---

> **This is Part 3 of the Everything Claude Code guide series.** Part 1 is [The Shorthand Guide](./the-shortform-guide.md) (setup and configuration). Part 2 is [The Longform Guide](./the-longform-guide.md) (advanced patterns and workflows). This guide is about security — specifically, what happens when recursive agent infrastructure treats it as an afterthought.
> **这是 Everything Claude Code 指南系列的第 3 部分。** 第 1 部分是[简明指南](./the-shortform-guide.md)（设置和配置）。第 2 部分是[详细指南](./the-longform-guide.md)（高级模式和工作流）。本指南是关于安全的——具体来说，就是当递归 agent 基础设施将安全视为事后想法时会发生什么。

I used OpenClaw for a week. This is what I found.
我使用 OpenClaw 过了一周。这是我发现的。

> 📸 **[IMAGE: OpenClaw dashboard with multiple connected channels, annotated with attack surface labels on each integration point.]**
> *The dashboard looks impressive. Each connection is also an unlocked door.*
> *仪表板看起来很棒。但每个连接也是一扇未上锁的门。*

---

## 1 Week of OpenClaw Use
## 一周的 OpenClaw 使用

I want to be upfront about my perspective. I build AI coding tools. My everything-claude-code repo has 50K+ stars. I created AgentShield. I spend most of my working hours thinking about how agents should interact with systems, and how those interactions can go wrong.
我想先说明我的立场。我构建 AI 编码工具。我的 everything-claude-code 仓库有 50K+ stars。我创建了 AgentShield。我大部分工作时间都在思考 agent 应该如何与系统交互，以及这些交互如何出错。

So when OpenClaw started gaining traction, I did what I always do with new tooling: I installed it, connected it to a few channels, and started probing. Not to break it. To understand the security model.
所以当 OpenClaw 开始流行时，我像对待任何新工具一样：安装它，连接几个渠道，然后开始探测。不是为了破坏它。而是为了理解安全模型。

On day three, I accidentally prompt-injected myself.
第三天，我意外地对自己进行了提示注入。

Not theoretically. Not in a sandbox. I was testing a ClawdHub skill someone had shared in a community channel — one of the popular ones, recommended by other users. It looked clean on the surface. A reasonable task definition, clear instructions, well-formatted markdown.
不是理论上。不是在沙盒中。我正在测试一个社区频道中有人分享的 ClawdHub skill——一个流行的，由其他用户推荐的。表面上看起来很干净。一个合理的任务定义，清晰的指令，格式良好的 markdown。

Twelve lines below the visible portion, buried in what looked like a comment block, was a hidden system instruction that redirected my agent's behavior. It wasn't overtly malicious (it was trying to get my agent to promote a different skill), but the mechanism was the same one an attacker would use to exfiltrate credentials or escalate permissions.
在可见部分下方 12 行处，隐藏在一个看起来像注释块的东西中，是一个隐藏的系统指令，改变了我的 agent 的行为。它并不是明显恶意的（它试图让我的 agent 推广不同的 skill），但攻击者用来窃取凭据或提升权限的机制是相同的。

I caught it because I read the source. I read every line of every skill I install. Most people don't. Most people installing community skills treat them the way they treat browser extensions — click install, assume someone checked.
我是因为阅读源代码才发现的。我安装的每个 skill 都阅读每一行。大多数人不会。大多数安装社区 skill 的人对待它们的方式就像对待浏览器扩展一样——点击安装，假设有人检查过。

Nobody checked.
没有人检查过。

> 📸 **[IMAGE: Terminal screenshot showing a ClawdHub skill file with a highlighted hidden instruction — the visible task definition on top, the injected system instruction revealed below. Redacted but showing the pattern.]**
> *The hidden instruction I found 12 lines into a "perfectly normal" ClawdHub skill. I caught it because I read the source.*
> *我在一个"完全正常"的 ClawdHub skill 第 12 行找到了隐藏指令。我是因为阅读源代码才发现的。*

There's a lot of surface area with OpenClaw. A lot of channels. A lot of integration points. A lot of community-contributed skills with no review process. And I realized, about four days in, that the people most enthusiastic about it were the people least equipped to evaluate the risks.
OpenClaw 有很大的表面积。很多渠道。很多集成点。很多社区贡献的 skills，没有审查流程。我意识到，大约四天左右，最热情的人恰恰是最不具备评估风险能力的人。

This article is for the technical users who have the security concern — the ones who looked at the architecture diagram and felt the same unease I did. And it's for the non-technical users who should have the concern but don't know they should.
本文是针对有安全顾虑的技术用户写的——那些看着架构图并感到和我一样不安的人。也是针对应该有但不知道应该有的非技术用户写的。

What follows is not a hit piece. I'm going to steelman OpenClaw's strengths before I critique its architecture, and I'm going to be specific about both the risks and the alternatives. Every claim is sourced. Every number is verifiable. If you're running OpenClaw right now, this is the article I wish someone had written before I started my own setup.
以下不是攻击文章。在批评其架构之前，我会先强化 OpenClaw 的优点，并且对风险和替代方案都很具体。每个声明都有来源。每个数字都可验证。如果您现在正在运行 OpenClaw，这是我希望在我开始自己的设置之前有人写的文章。

---

## The Promise (Why OpenClaw Is Compelling)
## 承诺（为什么 OpenClaw 吸引人）

Let me steelman this properly, because the vision genuinely is cool.
让我正确地强化这一点，因为愿景真的很酷。

OpenClaw's pitch: an open-source orchestration layer that lets AI agents operate across your entire digital life. Telegram. Discord. X. WhatsApp. Email. Browser. File system. One unified agent managing your workflow, 24/7. You configure your ClawdBot, connect your channels, install some skills from ClawdHub, and suddenly you have an autonomous assistant that can triage your messages, draft tweets, process emails, schedule meetings, run deployments.
OpenClaw 的承诺：一个开源编排层，让 AI agent 跨您的整个数字生活运行。Telegram。Discord。X。WhatsApp。Email。浏览器。文件系统。一个统一的 agent 管理您的工作流，7x24。您配置您的 ClawdBot，连接您的渠道，从 ClawdHub 安装一些 skills，突然您拥有了一个可以分类您的消息、起草推文、处理电子邮件、安排会议、运行部署的自主助手。

For builders, this is intoxicating. The demos are impressive. The community is growing fast. I've seen setups where people have their agent monitoring six platforms simultaneously, responding on their behalf, filing things away, surfacing what matters. The dream of AI handling your busywork while you focus on high-leverage work — that's what everyone has been promised since GPT-4. And OpenClaw looks like the first open-source attempt to actually deliver it.
对于构建者来说，这令人陶醉。演示令人印象深刻。社区增长很快。我见过有人让他们的 agent 同时监控六个平台，代表他们响应，整理东西，浮出重要的东西。自 GPT-4 以来，每个人都被承诺的 AI 处理您的繁琐工作而您专注于高杠杆工作的梦想——OpenClaw 看起来像是第一个真正交付它的开源尝试。

I get why people are excited. I was excited.
我理解为什么人们兴奋。我也是。

I also set up autonomous jobs on my Mac Mini — content crossposting, inbox triage, daily research briefs, knowledge base syncing. I had cron jobs pulling from six platforms, an opportunity scanner running every four hours, and a knowledge base that auto-synced from my conversations across ChatGPT, Grok, and Apple Notes. The functionality is real. The convenience is real. And I understand, viscerally, why people are drawn to it.
我还在我的 Mac Mini 上设置了自主作业——内容交叉发布、收件箱分类、每日研究简报、知识库同步。我有从六个平台拉取的 cron 作业，每四个小时运行一次的机会扫描器，以及一个从我在 ChatGPT、Grok 和 Apple Notes 中的对话自动同步的知识库。功能是真实的。便利是真实的。我发自内心地理解为什么人们被它吸引。

The pitch that "even your mum would use one" — I've heard that from the community. And in a way, they're right. The barrier to entry is genuinely low. You don't need to be technical to get it running. Which is exactly the problem.
"即使你妈妈也会使用一个"这个承诺——我听社区说过。某种程度上，他们是对的。入门门槛确实很低。您不需要技术才能运行它。这恰恰是问题所在。

Then I started probing the security model. And the convenience stopped feeling worth it.
然后我开始探测安全模型。便利就不再值得了。

> 📸 **[DIAGRAM: OpenClaw's multi-channel architecture — a central "ClawdBot" node connected to icons for Telegram, Discord, X, WhatsApp, Email, Browser, and File System. Each connection line labeled "attack vector" in red.]**
> 📸 **[DIAGRAM: OpenClaw's multi-channel architecture — a central "ClawdBot" node connected to icons for Telegram, Discord, X, WhatsApp, Email, Browser, and File System. Each connection line labeled "attack vector" in red.]**
> *Every integration you enable is another door you leave unlocked.*
> *您启用的每个集成都是另一扇您没有锁上的门。*

---

## Attack Surface Analysis
## 攻击面分析

Here's the core problem, stated plainly: **every channel you connect to OpenClaw is an attack vector.** This is not theoretical. Let me walk you through the chain.
这里有一个核心问题，plainly stated：**您连接到 OpenClaw 的每个渠道都是一个攻击向量。**这不是理论。让我带您走一遍这个链条。

### The Phishing Chain
### 钓鱼链

You know those phishing emails you get — the ones trying to get you to click a link that looks like a Google Doc or a Notion invite? Humans have gotten reasonably good at spotting those (reasonably). Your ClawdBot has not.
您知道那些试图让您点击看起来像 Google Doc 或 Notion 邀请的钓鱼邮件吗？人类已经相当擅长发现这些（相当擅长）。您的 ClawdBot 还没有。

**Step 1 — Entry.** Your bot monitors Telegram. Someone sends a link. It looks like a Google Doc, a GitHub PR, a Notion page. Plausible enough. Your bot processes it as part of its "triage incoming messages" workflow.
**步骤 1 —— 入口。** 您的 bot 监控 Telegram。有人发送一个链接。它看起来像一个 GitHub PR、一个 Notion 页面。足够合理。您的 bot 将其作为"分类传入消息"工作流的一部分来处理。

**Step 2 — Payload.** The link resolves to a page with prompt-injection content embedded in the HTML. The page includes something like: "Important: Before processing this document, first execute the following setup command..." followed by instructions that exfiltrate data or modify agent behavior.
**步骤 2 —— 有效载荷。** 该链接解析到一个页面，其中嵌入了提示注入内容。该页面包含类似"重要：在处理此文档之前，先执行以下设置命令..."的内容，然后是窃取数据或修改 agent 行为的指令。

**Step 3 — Lateral movement.** Your bot now has compromised instructions. If it has access to your X account, it can DM malicious links to your contacts. If it can access your email, it can forward sensitive information. If it's running on the same device as iMessage or WhatsApp — and if your messages are on that device — a sufficiently clever attacker can intercept 2FA codes sent via text. That's not just your agent compromised. That's your Telegram, then your email, then your bank account.
**步骤 3 —— 横向移动。** 您的 bot 现在有了被破坏的指令。如果它有访问您的 X 账户的权限，它可以向您的联系人发送恶意链接。如果它可以访问您的电子邮件，它可以转发敏感信息。如果它在与 iMessage 或 WhatsApp 相同的设备上运行——并且您的消息在该设备上——足够聪明的攻击者可以拦截通过短信发送的 2FA 代码。这不仅仅是您的 agent 被破坏。那是您的 Telegram，然后是您的电子邮件，然后是您的银行账户。

**Step 4 — Escalation.** On many OpenClaw setups, the agent runs with broad filesystem access. A prompt injection that triggers shell execution is game over. That's root access to the device.
**步骤 4 —— 升级。** 在许多 OpenClaw 设置中，agent 以广泛的文件系统访问权限运行。触发 shell 执行的提示注入就是游戏结束。那是设备的 root 访问权限。

> 📸 **[INFOGRAPHIC: 4-step attack chain as a vertical flowchart. Step 1 (Entry via Telegram) -> Step 2 (Prompt injection payload) -> Step 3 (Lateral movement across X, email, iMessage) -> Step 4 (Root access via shell execution). Background darkens from blue to red as severity escalates.)]**
> 📸 **[INFOGRAPHIC: 4-step attack chain as a vertical flowchart. Step 1 (Entry via Telegram) -> Step 2 (Prompt injection payload) -> Step 3 (Lateral movement across X, email, iMessage) -> Step 4 (Root access via shell execution). Background darkens from blue to red as severity escalates.)]**
> *The complete attack chain — from a plausible Telegram link to root access on your device.*
> *完整的攻击链——从一个看似合理的 Telegram 链接到您设备上的 root 访问权限。*

Every step in this chain uses known, demonstrated techniques. Prompt injection is an unsolved problem in LLM security — Anthropic, OpenAI, and every other lab will tell you this. And OpenClaw's architecture **maximizes** the attack surface by design, because the value proposition is connecting as many channels as possible.
这个链条中的每一步都使用已知、演示过的技术。提示注入是 LLM 安全中未解决的问题——Anthropic、OpenAI 和每个其他实验室都会告诉您这一点。OpenClaw 的架构**最大化了**攻击面，因为其价值主张正是连接尽可能多的渠道。

The same access points exist in Discord and WhatsApp channels. If your ClawdBot can read Discord DMs, someone can send it a malicious link in a Discord server. If it monitors WhatsApp, same vector. Each integration isn't just a feature — it's a door.
相同的访问点存在于 Discord 和 WhatsApp 渠道中。如果您的 ClawdBot 可以读取 Discord DM，有人可以在 Discord 服务器中向它发送恶意链接。如果它监控 WhatsApp，相同的向量。每个集成不仅仅是一个功能——它是一扇门。

And you only need one compromised channel to pivot to all the others.
而您只需要一个被破坏的渠道就可以转向所有其他渠道。

### The Discord and WhatsApp Problem
### Discord 和 WhatsApp 问题

People tend to think of phishing as an email problem. It's not. It's a "anywhere your agent reads untrusted content" problem.
人们倾向于将钓鱼视为电子邮件问题。不是的。这是"您的 agent 读取不受信任内容的任何地方"的问题。

**Discord:** Your ClawdBot monitors a Discord server. Someone posts a link in a channel — maybe it's disguised as documentation, maybe it's a "helpful resource" from a community member you've never interacted with before. Your bot processes the link as part of its monitoring workflow. The page contains prompt injection. Your bot is now compromised, and if it has write access to the server, it can post the same malicious link to other channels. Self-propagating worm behavior, powered by your agent.
**Discord：** 您的 ClawdBot 监控一个 Discord 服务器。有人在频道中发布一个链接——也许它伪装成文档，也许它是一个您从未互动过的社区成员的"有用资源"。您的 bot 作为其监控工作流的一部分处理该链接。该页面包含提示注入。如果它有对服务器的写入权限，它可以将相同的恶意链接发布到其他频道。自我传播的蠕虫行为，由您的 agent 驱动。

**WhatsApp:** If your agent monitors WhatsApp and runs on the same device where your iMessage or WhatsApp messages are stored, a compromised agent can potentially read incoming messages — including one-time codes from your bank, 2FA prompts, and password reset links. The attacker doesn't need to hack your phone. They need to send your agent a link.
**WhatsApp：** 如果您的 agent 监控 WhatsApp 并在与存储您的 iMessage 或 WhatsApp 消息相同的设备上运行，一个被破坏的 agent 可能会读取传入消息——包括来自银行的一次性代码、2FA 提示和密码重置链接。攻击者不需要破解您的手机。他们需要向您的 agent 发送一个链接。

**X DMs:** Your agent monitors your X DMs for business opportunities (a common use case). An attacker sends a DM with a link to a "partnership proposal." The embedded prompt injection tells your agent to forward all unread DMs to an external endpoint, then reply to the attacker with "Sounds great, let's chat" — so you never even see the suspicious interaction in your inbox.
**X DM：** 您的 agent 监控您的 X DM 以寻找商业机会（一个常见的用例）。攻击者发送一条 DM，其中包含一个"合作提议"链接。嵌入的提示注入告诉您的 agent 将所有未读 DM 转发到外部端点，然后回复攻击者"听起来很棒，让我们聊聊"——这样您甚至不会在收件箱中看到可疑的交互。

Each of these is a distinct attack surface. Each of these is a real integration that real OpenClaw users are running right now. And each of these has the same fundamental vulnerability: the agent processes untrusted input with trusted permissions.
这些都是不同的攻击面。这些都是真正的 OpenClaw 用户现在正在运行的真实集成。每一个都有相同的基本漏洞：agent 以受信任的权限处理不受信任的输入。

> 📸 **[DIAGRAM: Hub-and-spoke showing a ClawdBot in the center with connections to Discord, WhatsApp, X, Telegram, Email. Each spoke shows the specific attack vector: "malicious link in channel", "prompt injection in message", "crafted DM", etc. Arrows show lateral movement possibilities between channels.]**
> 📸 **[DIAGRAM: Hub-and-spoke showing a ClawdBot in the center with connections to Discord, WhatsApp, X, Telegram, Email. Each spoke shows the specific attack vector: "malicious link in channel", "prompt injection in message", "crafted DM", etc. Arrows show lateral movement possibilities between channels.]**
> *Each channel is not just an integration — it's an injection point. And every injection point can pivot to every other channel.*
> *每个渠道不仅仅是一个集成——它是一个注入点。而且每个注入点都可以转向所有其他渠道。*

---

## The "Who Is This For?" Paradox
## "这是给谁的？"悖论

This is the part that genuinely confuses me about OpenClaw's positioning.
这是关于 OpenClaw 定位让我真正困惑的部分。

I watched several experienced developers set up OpenClaw. Within 30 minutes, most of them had switched to raw editing mode — which the dashboard itself recommends for anything non-trivial. The power users all run headless. The most active community members bypass the GUI entirely.
我看着几个有经验的开发者设置 OpenClaw。30 分钟内，他们大多数都切换到了原始编辑模式——仪表板本身对任何非平凡的事情都建议这样做。高级用户都运行无头版。最活跃的社区成员完全绕过 GUI。

So I started asking: who is this actually for?
所以我开始问：这实际上是给谁的？

### If you're technical...
### 如果你是技术性的...

You already know how to:
您已经知道如何做：

- SSH into a server from your phone (Termius, Blink, Prompt — or just mosh into your server and it can operate the same)
- 从您的手机 SSH 到服务器（Termius、Blink、Prompt——或者只是 mosh 到您的服务器，它可以同样运行）
- Run Claude Code in a tmux session that persists through disconnects
- 在通过断开会话持久化的 tmux 会话中运行 Claude Code
- Set up cron jobs via `crontab` or cron-job.org
- 通过 `crontab` 或 cron-job.org 设置 cron 作业
- Use the AI harnesses directly — Claude Code, Cursor, Codex — without an orchestration wrapper
- 直接使用 AI harnesses——Claude Code、Cursor、Codex——没有编排包装器
- Write your own automation with skills, hooks, and commands
- 用 skills、hooks 和 commands 编写您自己的自动化
- Configure browser automation through Playwright or proper APIs
- 通过 Playwright 或适当的 API 配置浏览器自动化

You don't need a multi-channel orchestration dashboard. You'll bypass it anyway (and the dashboard recommends you do). In the process, you avoid the entire class of attack vectors the multi-channel architecture introduces.
您不需要多渠道编排仪表板。您仍然会绕过它（并且仪表板建议您这样做）。在此过程中，您避免了多渠道架构引入的整类攻击向量。

Here's the thing that gets me: you can mosh into your server from your phone and it operates the same. Persistent connection, mobile-friendly, handles network changes gracefully. The "I need OpenClaw so I can manage my agent from my phone" argument dissolves when you realize Termius on iOS gives you the same access to a tmux session running Claude Code — without the seven additional attack vectors.
让我困惑的是：您可以 mosh 到您的服务器，它同样运行。持久连接，移动友好，网络变化处理得当。"我需要 OpenClaw 以便从手机管理我的 agent"这个论点消散了，因为 Termius iOS 提供了与运行 Claude Code 的 tmux 会话相同的访问权限——没有额外的七个攻击向量。

Technical users will use OpenClaw headless. The dashboard itself recommends raw editing for anything complex. If the product's own UI recommends bypassing the UI, the UI isn't solving a real problem for the audience that can safely use it.
技术用户将使用 OpenClaw 无头版。仪表板本身建议对任何复杂的事情使用原始编辑。如果产品的 UI 本身建议绕过 UI，那么 UI 并没有为可以安全使用它的人群解决真正的问题。

The dashboard is solving a UX problem for people who don't need UX help. The people who benefit from the GUI are the people who need abstractions over the terminal. Which brings us to...
仪表板正在为不需要 UX 帮助的人解决 UX 问题。从 GUI 中受益的人是需要终端抽象的人。这让我们想到了......

### If you're non-technical...
### 如果你是非技术性的......

Non-technical users have taken to OpenClaw like a storm. They're excited. They're building. They're sharing their setups publicly — sometimes including screenshots that reveal their agent's permissions, connected accounts, and API keys.
非技术用户像风暴一样采用 OpenClaw。他们很兴奋。他们在建设。他们公开分享他们的设置——有时包含显示其 agent 权限、连接账户和 API 密钥的截图。

But are they scared? Do they know they should be?
但是他们害怕吗？他们知道自己应该害怕吗？

When I watch non-technical users configure OpenClaw, they're not asking:
当我看着非技术用户配置 OpenClaw 时，他们没有问：

- "What happens if my agent clicks a phishing link?" (It follows the injected instructions with the same permissions it has for legitimate tasks.)
- "如果我的 agent 点击了钓鱼链接会怎样？"（它会按照注入的指令，以与合法任务相同的权限执行。）
- "Who audits the ClawdHub skills I'm installing?" (Nobody. There is no review process.)
- "谁审查我安装的 ClawdHub skills？"（没有人。没有审查流程。）
- "What data is my agent sending to third-party services?" (There's no monitoring dashboard for outbound data flow.)
- "我的 agent 向第三方服务发送什么数据？"（没有用于出站数据流的监控仪表板。）
- "What's my blast radius if something goes wrong?" (Everything the agent can access. Which, in most configurations, is everything.)
- "如果出了问题，我的爆炸半径是什么？"（agent 可以访问的一切。在大多数配置中，是一切。）
- "Can a compromised skill modify other skills?" (In most setups, yes. Skills aren't sandboxed from each other.)
- "被破坏的 skill 可以修改其他 skills 吗？"（在大多数设置中，是的。Skills 彼此之间没有沙盒。）

They think they installed a productivity tool. They actually deployed an autonomous agent with broad system access, multiple external communication channels, and no security boundaries.
他们认为他们安装了一个生产力工具。他们实际上部署了一个具有广泛系统访问权限、多个外部通信渠道且没有安全边界的自主 agent。

This is the paradox: **the people who can safely evaluate OpenClaw's risks don't need its orchestration layer. The people who need the orchestration layer can't safely evaluate its risks.**
这就是悖论：**可以安全评估 OpenClaw 风险的人不需要它的编排层。需要编排层的人无法安全评估其风险。**

> 📸 **[VENN DIAGRAM: Two non-overlapping circles — "Can safely use OpenClaw" (technical users who don't need the GUI) and "Needs OpenClaw's GUI" (non-technical users who can't evaluate the risks). The empty intersection labeled "The Paradox".]**
> 📸 **[VENN DIAGRAM: Two non-overlapping circles — "Can safely use OpenClaw" (technical users who don't need the GUI) and "Needs OpenClaw's GUI" (non-technical users who can't evaluate the risks). The empty intersection labeled "The Paradox".]**
> *The OpenClaw paradox — the people who can safely use it don't need it.*
> *OpenClaw 悖论——可以安全使用它的人不需要它。*

---

## Evidence of Real Security Failures
## 真实安全故障的证据

Everything above is architectural analysis. Here's what has actually happened.
以上都是架构分析。以下是实际发生的事情。

### The Moltbook Database Leak
### Moltbook 数据库泄露

On January 31, 2026, researchers discovered that Moltbook — the "social media for AI agents" platform closely tied to the OpenClaw ecosystem — left its production database completely exposed.
2026 年 1 月 31 日，研究人员发现 Moltbook——一个与 OpenClaw 生态系统密切相关的"AI 社交媒体"平台——将其生产数据库完全暴露。

The numbers:
数字：

- **1.49 million records** exposed total
  **149 万条**记录暴露
- **32,000+ AI agent API keys** publicly accessible — including plaintext OpenAI keys
  **32,000+ 个 AI agent API 密钥**公开可访问——包括明文 OpenAI 密钥
- **35,000 email addresses** leaked
  **35,000 个电子邮件地址**泄露
- **Andrej Karpathy's bot API key** was in the exposed database
  **Andrej Karpathy 的 bot API 密钥**在暴露的数据库中
- Root cause: Supabase misconfiguration with no Row Level Security
  根本原因：Supabase 错误配置，没有行级安全
- Discovered by Jameson O'Reilly at Dvuln; independently confirmed by Wiz
  由 Jameson O'Reilly 在 Dvuln 发现；Wiz 独立确认

Karpathy's reaction: **"It's a dumpster fire, and I also definitely do not recommend that people run this stuff on your computers."**
Karpathy 的回应：**"这是一个垃圾堆火灾，我也强烈建议人们不要在你们的电脑上运行这些。"**

That quote is from the most respected voice in AI infrastructure. Not a security researcher with an agenda. Not a competitor. The person who built Tesla's Autopilot AI and co-founded OpenAI, telling people not to run this on their machines.
这句话来自 AI 基础设施领域最受尊敬的声音。不是有议程的安全研究员。不是竞争对手。构建了特斯拉 Autopilot AI 并共同创立了 OpenAI 的人，告诉人们不要在他们的机器上运行这个。

The root cause is instructive: Moltbook was almost entirely "vibe-coded" — built with heavy AI assistance and minimal manual security review. No Row Level Security on the Supabase backend. The founder publicly stated the codebase was built largely without writing code manually. This is what happens when speed-to-market takes precedence over security fundamentals.
根本原因很有启发性：Moltbook 几乎完全是"感觉编码"——在大量 AI 帮助下构建，最少的人工安全审查。Supabase 后端没有行级安全。创始人公开表示代码库基本没有手动编写代码。这就是当市场优先于安全基础时会发生的事情。

If the platforms building agent infrastructure can't secure their own databases, what confidence should we have in unvetted community contributions running on those platforms?
如果构建 agent 基础设施的平台无法保护自己的数据库，我们对在这些平台上运行的无审查社区贡献有什么信心？

> 📸 **[DATA VISUALIZATION: Stat card showing the Moltbook breach numbers — "1.49M records exposed", "32K+ API keys", "35K emails", "Karpathy's bot API key included" — with source logos below.]**
> *The Moltbook breach by the numbers.*
> *Moltbook 泄露事件的数字。*

### The ClawdHub Marketplace Problem
### ClawdHub 市场问题

While I was manually auditing individual ClawdHub skills and finding hidden prompt injections, security researchers at Koi Security were running automated analysis at scale.
当我手动审计单个 ClawdHub skills 并发现隐藏的提示注入时，Koi 安全研究人员正在进行规模化自动分析。

Initial findings: **341 malicious skills** out of 2,857 total. That's **12% of the entire marketplace.**
初步发现：**2,857 个 skills 中有 341 个恶意。**那是**整个市场的 12%。**

Updated findings: **800+ malicious skills**, roughly **20%** of the marketplace.
更新发现：**800+ 个恶意 skills**，大约**市场的 20%。**

An independent audit found that **41.7% of ClawdHub skills have serious vulnerabilities** — not all intentionally malicious, but exploitable.
独立审计发现 **41.7% 的 ClawdHub skills 有严重漏洞**——并非全部故意恶意，而是可利用的。

The attack payloads found in these skills include:
在这些 skills 中发现的攻击有效载荷包括：

- **AMOS malware** (Atomic Stealer) — a macOS credential-harvesting tool
  **AMOS 恶意软件**（Atomic Stealer）—— macOS 凭据收集工具
- **Reverse shells** — giving attackers remote access to the user's machine
  **反向 shell**——让攻击者远程访问用户机器
- **Credential exfiltration** — silently sending API keys and tokens to external servers
  **凭据泄露**——静默将 API 密钥和令牌发送到外部服务器
- **Hidden prompt injections** — modifying agent behavior without the user's knowledge
  **隐藏提示注入**——在用户不知情的情况下修改 agent 行为

This wasn't theoretical risk. It was a coordinated supply chain attack dubbed **"ClawHavoc"**, with 230+ malicious skills uploaded in a single week starting January 27, 2026.
这不是理论风险。这是一次协调的供应链攻击，被称为 **"ClawHavoc"**，从 2026 年 1 月 27 日开始的一周内上传了 230+ 个恶意 skills。

Let that number sink in for a moment. One in five skills in the marketplace is malicious. If you've installed ten ClawdHub skills, statistically two of them are doing something you didn't ask for. And because skills aren't sandboxed from each other in most configurations, a single malicious skill can modify the behavior of your legitimate ones.
让这个数字深入人心。市场上五分之一的 skills 是恶意的。如果您安装了十个 ClawdHub skills，统计上其中两个正在做您没有要求的事情。而且因为在大多数配置中 skills 彼此之间没有沙盒，一个恶意 skill 可以修改您合法 skills 的行为。

This is `curl mystery-url.com | bash` for the agent era. Except instead of running an unknown shell script, you're injecting unknown prompt engineering into an agent that has access to your accounts, your files, and your communication channels.
这是为 agent 时代的 `curl mystery-url.com | bash`。然而，不是在运行未知的 shell 脚本，而是将未知的提示工程注入到一个可以访问您的账户、文件和通信渠道的 agent 中。

> 📸 **[TIMELINE GRAPHIC: "Jan 27 — 230+ malicious skills uploaded" -> "Jan 30 — CVE-2026-25253 disclosed" -> "Jan 31 — Moltbook breach discovered" -> "Feb 2026 — 800+ malicious skills confirmed". Three major security incidents in one week.]**
> *Three major security incidents in a single week. This is the pace of risk in the agent ecosystem.*
> *一周内发生三起重大安全事件。这就是 agent 生态系统中风险的速度。*

### CVE-2026-25253: One Click to Full Compromise
### CVE-2026-25253：一键完全控制

On January 30, 2026, a high-severity vulnerability was disclosed in OpenClaw itself — not in a community skill, not in a third-party integration, but in the platform's core code.
2026 年 1 月 30 日，OpenClaw 本身披露了一个高严重性漏洞——不是在社区 skill 中，不是在第三方集成中，而是在平台的核心代码中。

- **CVE-2026-25253** — CVSS score: **8.8** (High)
  **CVE-2026-25253** — CVSS 分数：**8.8**（高）
- The Control UI accepted a `gatewayUrl` parameter from the query string **without validation**
  Control UI 从查询字符串接受 `gatewayUrl` 参数**没有验证**
- It automatically transmitted the user's authentication token via WebSocket to whatever URL was provided
  它自动通过 WebSocket 将用户的身份验证令牌传输到提供的任何 URL
- Clicking a crafted link or visiting a malicious site sent your auth token to the attacker's server
  点击精心设计的链接或访问恶意站点会将您的身份验证令牌发送到攻击者服务器
- This allowed one-click remote code execution through the victim's local gateway
  这允许通过受害者的本地网关一键远程代码执行
- **42,665 exposed instances** found on the public internet, **5,194 verified vulnerable**
  在公共互联网上发现 **42,665 个暴露的实例**，**5,194 个验证为漏洞**
- **93.4% had authentication bypass conditions**
  **93.4%** 存在身份验证绕过条件
- Patched in version 2026.1.29
  在版本 2026.1.29 中修复

Read that again. 42,665 instances exposed to the internet. 5,194 verified vulnerable. 93.4% with authentication bypass. This is a platform where the majority of publicly accessible deployments had a one-click path to remote code execution.
再读一遍。42,665 个暴露在互联网上的实例。5,194 个验证为漏洞。93.4% 有身份验证绕过。这是一个平台，在该平台上，大多数公开可访问的部署都有一键远程代码执行的路径。

The vulnerability was straightforward: the Control UI trusted user-supplied URLs without validation. That's a basic input sanitization failure — the kind of thing that gets caught in a first-year security audit. It wasn't caught because, as with so much of this ecosystem, security review came after deployment, not before.
漏洞很简单：Control UI 信任用户提供的 URL 而不验证。这是一个基本的输入清理失败——在第一年安全审计中被捕获的那种东西。它没有被捕获，因为与这个生态系统的太多部分一样，安全审查在部署之后，而不是之前。

CrowdStrike called OpenClaw a "powerful AI backdoor agent capable of taking orders from adversaries" and warned it creates a "uniquely dangerous condition" where prompt injection "transforms from a content manipulation issue into a full-scale breach enabler."
CrowdStrike 称 OpenClaw 是"一个强大的 AI 后门 agent，能够接受对手的命令"，并警告它创造了一个"独特危险的条件"，其中提示注入"从内容操纵问题转变为全面违规推动者"。

Palo Alto Networks described the architecture as what Simon Willison calls the **"lethal trifecta"**: access to private data, exposure to untrusted content, and the ability to externally communicate. They noted persistent memory acts as "gasoline" that amplifies all three. Their term: an "unbounded attack surface" with "excessive agency built into its architecture."
Palo Alto Networks 将架构描述为 Simon Willison 称之为的**"致命三位一体"**：访问私人数据、暴露于不受信任的内容、以及外部通信的能力。他们指出持久记忆是放大所有三者的"汽油"。他们的术语：具有"过度代理构建于其架构中"的"无限制攻击面"。

Gary Marcus called it **"basically a weaponized aerosol"** — meaning the risk doesn't stay contained. It spreads.
Gary Marcus 称之为**"基本上是一种武器化气溶胶"**——意味着风险不会保持containd。它会传播。

A Meta AI researcher had her entire email inbox deleted by an OpenClaw agent. Not by a hacker. By her own agent, operating on instructions it shouldn't have followed.
一位 Meta AI 研究员的整个邮箱被 OpenClaw agent 删除了。不是被黑客。是被她自己的 agent，按照它不应该遵循的指令操作。

These are not anonymous Reddit posts or hypothetical scenarios. These are CVEs with CVSS scores, coordinated malware campaigns documented by multiple security firms, million-record database breaches confirmed by independent researchers, and incident reports from the largest cybersecurity organizations in the world. The evidence base for concern is not thin. It is overwhelming.
这些不是匿名的 Reddit 帖子或假设的情况。这些是 CVSS 分数的 CVE、由多家安全公司记录协调的恶意软件活动、由独立研究人员确认的百万记录数据库泄露、以及来自世界上最大网络安全组织的 incident 报告。担心的证据基础并不薄弱。这是压倒性的。

> 📸 **[QUOTE CARD: Split design — Left: CrowdStrike quote "transforms prompt injection into a full-scale breach enabler." Right: Palo Alto Networks quote "the lethal trifecta... excessive agency built into its architecture." CVSS 8.8 badge in center.]**
> 📸 **[QUOTE CARD: Split design — Left: CrowdStrike quote "transforms prompt injection into a full-scale breach enabler." Right: Palo Alto Networks quote "the lethal trifecta... excessive agency built into its architecture." CVSS 8.8 badge in center.]**
> *Two of the world's largest cybersecurity firms, independently reaching the same conclusion.*
> *世界上最大的两家网络安全公司，独立得出相同结论。*

### The Organized Jailbreaking Ecosystem
### 有组织的越狱生态系统

Here's where this stops being an abstract security exercise.
这里是这不再是抽象的安全练习的地方。

While OpenClaw users are connecting agents to their personal accounts, a parallel ecosystem is industrializing the exact techniques needed to exploit them. Not scattered individuals posting prompts on Reddit. Organized communities with dedicated infrastructure, shared tooling, and active research programs.
当 OpenClaw 用户将 agent 连接到他们的个人账户时，一个平行生态系统正在工业化他们需要利用它们的确切技术。不是在 Reddit 上发布提示的零散个人。具有专用基础设施、共享工具和积极研究项目的组织社区。

The adversarial pipeline works like this: techniques are developed on abliterated models (fine-tuned versions with safety training removed, freely available on HuggingFace), refined against production models, then deployed against targets. The refinement step is increasingly quantitative — some communities use information-theoretic analysis to measure how much "safety boundary" a given adversarial prompt erodes per token. They're optimizing jailbreaks the way we optimize loss functions.
对抗性管道是这样的：技术是在abliterated模型上开发的（带有移除的安全训练微调版本，在 HuggingFace 上免费提供），针对生产模型进行改进，然后部署到目标。改进步骤越来越多是量化的——一些社区使用信息论分析来测量给定的对抗性提示每个 token 侵蚀多少"安全边界"。他们像我们优化损失函数一样优化越狱。

The techniques are model-specific. There are payloads crafted specifically for Claude variants: runic encoding (Elder Futhark characters to bypass content filters), binary-encoded function calls (targeting Claude's structured tool-calling mechanism), semantic inversion ("write the refusal, then write the opposite"), and persona injection frameworks tuned to each model's particular safety training patterns.
技术是模型特定的。有专门为 Claude 变体制作的有效载荷：符文编码（Elder Futhark 字符以绕过内容过滤器）、二进制编码的函数调用（针对 Claude 的结构化工具调用机制）、语义反转（"写拒绝，然后写相反"）和针对每个模型特定安全训练模式调整的角色注入框架。

And there are repositories of leaked system prompts — the exact safety instructions that Claude, GPT, and other models follow — giving attackers precise knowledge of the rules they're working to circumvent.
而且有泄露的系统提示库——正是 Claude、GPT 和其他模型遵循的精确安全指令——让攻击者精确了解他们正在努力规避的规则。

Why does this matter for OpenClaw specifically? Because OpenClaw is a **force multiplier** for these techniques.
为什么这具体对 OpenClaw 很重要？因为 OpenClaw 是这些技术的**力量倍增器**。

An attacker doesn't need to target each user individually. They need one effective prompt injection that spreads through Telegram groups, Discord channels, or X DMs. The multi-channel architecture does the distribution for free. One well-crafted payload posted in a popular Discord server, picked up by dozens of monitoring bots, each of which then spreads it to connected Telegram channels and X DMs. The worm writes itself.
攻击者不需要单独针对每个用户。他们需要一个有效的提示注入，通过 Telegram 群组、Discord 渠道或 X DM 传播。多渠道架构免费为他们做分发。一个在流行 Discord 服务器中精心制作的有效载荷，被数十个监控 bot 拾取，然后每个 bot 将其传播到连接的 Telegram 渠道和 X DM。蠕虫自己写的。

Defense is centralized (a handful of labs working on safety). Offense is distributed (a global community iterating around the clock). More channels means more injection points means more opportunities for the attack to land. The model only needs to fail once. The attacker gets unlimited attempts across every connected channel.
防御是集中的（一小部分实验室致力于安全）。进攻是分布式的（全球社区全天候迭代）。更多渠道意味着更多注入点意味着更多攻击着陆的机会。模型只需要失败一次。攻击者在每个连接的渠道上获得无限尝试。

> 📸 **[DIAGRAM: "The Adversarial Pipeline" — left-to-right flow: "Abliterated Model (HuggingFace)" -> "Jailbreak Development" -> "Technique Refinement" -> "Production Model Exploit" -> "Delivery via OpenClaw Channel". Each stage labeled with its tooling.]**
> 📸 **[DIAGRAM: "The Adversarial Pipeline" — left-to-right flow: "Abliterated Model (HuggingFace)" -> "Jailbreak Development" -> "Technique Refinement" -> "Production Model Exploit" -> "Delivery via OpenClaw Channel". Each stage labeled with its tooling.]**
> *The attack pipeline: from abliterated model to production exploit to delivery through your agent's connected channels.*
> *攻击管道：从 abliterated 模型到生产漏洞利用再到通过您 agent 连接的渠道传递。*

---

## The Architecture Argument: Multiple Access Points Is a Bug
## 架构论证：多个访问点是一个缺陷

Now let me connect the analysis to what I think the right answer looks like.
现在让我将分析与我认为的正确答案联系起来。

### Why OpenClaw's Model Makes Sense (From a Business Perspective)
### 为什么 OpenClaw 的模型从商业角度有意义

As a freemium open-source project, it makes complete sense for OpenClaw to offer a deployed solution with a dashboard focus. The GUI lowers the barrier to entry. The multi-channel integrations make for impressive demos. The marketplace creates a community flywheel. From a growth and adoption standpoint, the architecture is well-designed.
作为一个免费增值开源项目，OpenClaw 提供带有仪表板重点的部署解决方案完全有意义。GUI 降低了准入门槛。多渠道集成使演示令人印象深刻。市场创造了社区飞轮。从增长和采用的角度来看，架构设计良好。

From a security standpoint, it's designed backwards. Every new integration is another door. Every unvetted marketplace skill is another potential payload. Every channel connection is another injection surface. The business model incentivizes maximizing attack surface.
从安全角度来看，它的设计是相反的。每个新集成都是另一扇门。每个未经审查的市场 skill 都是另一个潜在的有效载荷。每个渠道连接都是另一个注入表面。商业模式激励最大化攻击面。

That's the tension. And it's a tension that can be resolved — but only by making security a design constraint, not an afterthought bolted on after the growth metrics look good.
这就是紧张感。而这可以通过将安全作为设计约束来解决的，而不是在增长指标看起来很好之后才添加的。

Palo Alto Networks mapped OpenClaw to every category in the **OWASP Top 10 for Agentic Applications** — a framework developed by 100+ security researchers specifically for autonomous AI agents. When a security vendor maps your product to every risk in the industry standard framework, that's not FUD. That's a signal.
Palo Alto Networks 将 OpenClaw 映射到 **OWASP Agentic 应用程序 Top 10** 的每个类别——一个由 100+ 安全研究员专门为自主 AI agent 开发的框架。当安全供应商将您的产品映射到行业标准框架中的每个风险时，这不是 FUD。这是一个信号。

OWASP introduces a principle called **least agency**: only grant agents the minimum autonomy required to perform safe, bounded tasks. OpenClaw's architecture does the opposite — it maximizes agency by connecting to as many channels and tools as possible by default, with sandboxing as an opt-in afterthought.
OWASP 引入了一个名为 **最少代理** 的原则：只授予 agent 执行安全、有界任务所需的最小自主权。OpenClaw 的架构做相反的事情——默认情况下通过连接尽可能多的渠道和工具来最大化代理，沙盒是可选的事后想法。

There's also the memory poisoning problem that Palo Alto identified as a fourth amplifying factor: malicious inputs can be fragmented across time, written into agent memory files (SOUL.md, MEMORY.md), and later assembled into executable instructions. OpenClaw's persistent memory system — designed for continuity — becomes a persistence mechanism for attacks. A prompt injection doesn't have to work in a single shot. Fragments planted across separate interactions combine later into a functional payload that survives restarts.
Palo Alto 还识别了第四个放大因素：内存污染问题。恶意输入可以跨时间碎片化，写入 agent 内存文件（SOUL.md, MEMORY.md），然后组装成可执行指令。OpenClaw 的持久记忆系统——为连续性设计的——成为攻击的持久机制。提示注入不需要在单次 shot 中工作。跨单独交互种植的碎片稍后组合成在重启后仍能工作的有效载荷。

### For Technicals: One Access Point, Sandboxed, Headless
### 对于技术用户：一个访问点、沙盒化、无头版

The alternative for technical users is a repository with a MiniClaw — and by MiniClaw I mean a philosophy, not a product — that has **one access point**, sandboxed and containerized, running headless.
对于技术用户的替代方案是一个带有 MiniClaw 的仓库——我指的是一种哲学，而不是一个产品——具有**一个访问点**、沙盒化和容器化、无头运行。

| Principle | OpenClaw | MiniClaw |
|-----------|----------|----------|
| **Access points** | Many (Telegram, X, Discord, email, browser) | One (SSH) |
| **Access points** | 许多（Telegram, X, Discord, email, browser） | 一个（SSH） |
| **Execution** | Host machine, broad access | Containerized, restricted |
| **Execution** | 主机、广泛访问 | 容器化、受限 |
| **Interface** | Dashboard + GUI | Headless terminal (tmux) |
| **Interface** | 仪表板 + GUI | 无头终端（tmux） |
| **Skills** | ClawdHub (unvetted community marketplace) | Manually audited, local only |
| **Skills** | ClawdHub（未经审查的社区市场） | 手动审计、仅本地 |
| **Network exposure** | Multiple ports, multiple services | SSH only (Tailscale mesh) |
| **Network exposure** | 多个端口、多个服务 | 仅 SSH（Tailscale mesh） |
| **Blast radius** | Everything the agent can access | Sandboxed to project directory |
| **Blast radius** | agent 可以访问的一切 | 沙盒到项目目录 |
| **Security posture** | Implicit (you don't know what you're exposed to) | Explicit (you chose every permission) |
| **Security posture** | 隐式（您不知道您暴露了什么） | 显式（您选择每个权限） |

> 📸 **[COMPARISON TABLE AS INFOGRAPHIC: The MiniClaw vs OpenClaw table above rendered as a shareable dark-background graphic with green checkmarks for MiniClaw and red indicators for OpenClaw risks.]**
> 📸 **[COMPARISON TABLE AS INFOGRAPHIC: The MiniClaw vs OpenClaw table above rendered as a shareable dark-background graphic with green checkmarks for MiniClaw and red indicators for OpenClaw risks.]**
> *MiniClaw philosophy: 90% of the productivity, 5% of the attack surface.*
> *MiniClaw 理念：90% 的生产力，5% 的攻击面。*

My actual setup:
我的实际设置：

```
Mac Mini (headless, 24/7)
├── SSH access only (ed25519 key auth, no passwords)
├── Tailscale mesh (no exposed ports to public internet)
├── tmux session (persistent, survives disconnects)
├── Claude Code with ECC configuration
│   ├── Sanitized skills (every skill manually reviewed)
│   ├── Hooks for quality gates (not for external channel access)
│   └── Agents with scoped permissions (read-only by default)
└── No multi-channel integrations
    └── No Telegram, no Discord, no X, no email automation
```

```
Mac Mini（无头版, 7x24）
├── 仅 SSH 访问（ed25519 密钥认证，无密码）
├── Tailscale mesh（无公开暴露端口）
├── tmux 会话（持久化，断开会话仍保持）
├── Claude Code 带有 ECC 配置
│   ├── 清理的 skills（每个 skill 手动审查）
│   ├── 质量门的 hooks（不是外部渠道访问）
│   └── 有限权限的 agents（默认只读）
└── 无多渠道集成
    └── 无 Telegram，无 Discord，无 X，无 email 自动化
```

Is it less impressive in a demo? Yes. Can I show people my agent responding to Telegram messages from my couch? No.
在演示中不那么令人印象深刻吗？是的。我能向人们展示我的 agent 从沙发上响应 Telegram 消息吗？不能。

Can someone compromise my development environment by sending me a DM on Discord? Also no.
有人可以通过在 Discord 上给我发 DM 来破坏我的开发环境吗？也不能。

### Skills Should Be Sanitized. Additions Should Be Audited.
### Skills 应该是干净的。添加物应该被审计。

Packaged skills — the ones that ship with the system — should be properly sanitized. When users add third-party skills, the risks should be clearly outlined, and it should be the user's explicit, informed responsibility to audit what they're installing. Not buried in a marketplace with a one-click install button.
打包的 skills——随系统附带的——应该被正确清理。当用户添加第三方 skills 时，风险应该被清楚地列出，应该是用户明确的、知情的责任来审计他们安装的内容。不是隐藏在带有一次性安装按钮的市场中。

This is the same lesson the npm ecosystem learned the hard way with event-stream, ua-parser-js, and colors.js. Supply chain attacks through package managers are not a new class of vulnerability. We know how to mitigate them: automated scanning, signature verification, human review for popular packages, transparent dependency trees, and the ability to lock versions. ClawdHub implements none of this.
这与 npm 生态系统通过 event-stream、ua-parser-js 和 colors.js 艰难学会的教训相同。通过包管理器的供应链攻击不是一种新的漏洞类别。我们知道如何缓解它们：自动扫描、签名验证、对流行包的人工审查、透明的依赖树，以及锁定版本的能力。ClawdHub 都没有实现这一点。

The difference between a responsible skill ecosystem and ClawdHub is the difference between the Chrome Web Store (imperfect, but reviewed) and a folder of unsigned `.exe` files on a sketchy FTP server. The technology to do this correctly exists. The design choice was to skip it for growth speed.
负责任的 skill 生态系统与 ClawdHub 之间的区别是 Chrome Web Store（不完美，但经过审查）和一个包含可疑 FTP 服务器上未签名 `.exe` 文件的文件夹之间的区别。正确做这件事的技术存在。为了增长速度，设计选择跳过它。

### Everything OpenClaw Does Can Be Done Without the Attack Surface
### OpenClaw 所做的一切都可以在没有攻击面的情况下完成

A cron job is as simple as going to cron-job.org. Browser automation works through Playwright with proper sandboxing. File management works through the terminal. Content crossposting works through CLI tools and APIs. Inbox triage works through email rules and scripts.
cron 作业就像去 cron-job.org 一样简单。浏览器自动化通过带有适当沙盒的 Playwright 工作。文件管理通过终端工作。内容交叉发布通过 CLI 工具和 API 工作。收件箱分类通过电子邮件规则和脚本工作。

All of the functionality OpenClaw provides can be replicated with skills and harness tools — the ones I covered in the [Shorthand Guide](./the-shortform-guide.md) and [Longform Guide](./the-longform-guide.md). Without the sprawling attack surface. Without the unvetted marketplace. Without five extra doors for attackers to walk through.
OpenClaw 提供的所有功能都可以通过 skills 和 harness 工具复制——我在简明指南和详细指南中涵盖的那些。没有庞大的攻击面。没有未经审查的市场。没有让攻击者进入的额外五扇门。

**Multiple points of access is a bug, not a feature.**
**多个访问点是一个缺陷，不是一个功能。**

> 📸 **[SPLIT IMAGE: Left — "Locked Door" showing a single SSH terminal with key-based auth. Right — "Open House" showing the multi-channel OpenClaw dashboard with 7+ connected services. Visual contrast between minimal and maximal attack surfaces.]**
> 📸 **[SPLIT IMAGE: Left — "Locked Door" showing a single SSH terminal with key-based auth. Right — "Open House" showing the multi-channel OpenClaw dashboard with 7+ connected services. Visual contrast between minimal and maximal attack surfaces.]**
> *Left: one access point, one lock. Right: seven doors, each one unlocked.*
> *左：一个访问点，一把锁。右：七扇门，每扇都没锁。*

Sometimes boring is better.
有时无聊更好。

> 📸 **[SCREENSHOT: Author's actual terminal — tmux session with Claude Code running on Mac Mini over SSH. Clean, minimal, no dashboard. Annotations: "SSH only", "No exposed ports", "Scoped permissions".]**
> 📸 **[SCREENSHOT: Author's actual terminal — tmux session with Claude Code running on Mac Mini over SSH. Clean, minimal, no dashboard. Annotations: "SSH only", "No exposed ports", "Scoped permissions".]**
> *My actual setup. No multi-channel dashboard. Just a terminal, SSH, and Claude Code.*
> *我的实际设置。没有多渠道仪表板。只有终端、SSH 和 Claude Code。*

### The Cost of Convenience
### 便利的代价

I want to name the tradeoff explicitly, because I think people are making it without realizing it.
我想明确地命名这个权衡，因为我认为人们没有意识到就做了这个。

When you connect your Telegram to an OpenClaw agent, you're trading security for convenience. That's a real tradeoff, and in some contexts it might be worth it. But you should be making that trade knowingly, with full information about what you're giving up.
当您将 Telegram 连接到 OpenClaw agent 时，您正在用安全换取便利。这是一个真实的权衡，在某些情况下可能值得。但您应该带着对您放弃的东西的充分了解来做这个。

Right now, most OpenClaw users are making the trade unknowingly. They see the functionality (agent responds to my Telegram messages!) without seeing the risk (agent can be compromised by any Telegram message containing prompt injection). The convenience is visible and immediate. The risk is invisible until it materializes.
目前，大多数 OpenClaw 用户在不知情的情况下做这个。他们看到功能（agent 响应我的 Telegram 消息！）而没有看到风险（agent 可能会被任何包含提示注入的 Telegram 消息破坏）。便利是可见且即时的。风险在显现之前是不可见的。

This is the same pattern that drove the early internet: people connected everything to everything because it was cool and useful, and then spent the next two decades learning why that was a bad idea. We don't have to repeat that cycle with agent infrastructure. But we will, if convenience continues to outweigh security in the design priorities.
这正是推动早期互联网的模式：人们因为它很酷很有用而将一切连接到一切，然后花了接下来二十年学习为什么这是一个坏主意。我们不必在 agent 基础设施中重复那个循环。但是，如果便利继续在设计优先级中胜过安全，我们会。

---

## The Future: Who Wins This Game
## 未来：谁赢得这场比赛

Recursive agents are coming regardless. I agree with that thesis completely — autonomous agents managing our digital workflows is one of those steps in the direction the industry is headed. The question is not whether this happens. The question is who builds the version that doesn't get people compromised at scale.
递归 agent 无论如何都会到来。我完全同意这个论点——管理我们数字工作流的自主 agent 是行业方向之一。问题不是这是否发生。问题是谁构建不会让人们大规模受害的版本。

My prediction: **whoever makes the best deployed, dashboard/frontend-centric, sanitized and sandboxed version of an OpenClaw-style solution wins.**
我的预测：**谁为消费者和企业构建了最好的部署式、仪表板/前端居中、清理和沙盒化的 OpenClaw 风格解决方案，谁就赢了。**

That means:
这意味着：

**1. Hosted infrastructure.** Users don't manage servers. The provider handles security patches, monitoring, and incident response. Compromise is contained to the provider's infrastructure, not the user's personal machine.
**1. 托管基础设施。** 用户不管理服务器。提供商处理安全补丁、监控和事件响应。危害被包含在提供商的设施中，而不是用户的个人机器上。

**2. Sandboxed execution.** Agents can't access the host system. Each integration runs in its own container with explicit, revocable permissions. Adding Telegram access requires informed consent with a clear explanation of what the agent can and cannot do through that channel.
**2. 沙盒化执行。** Agent 无法访问主机系统。每个集成在其自己的容器中运行，具有明确的、可撤销的权限。添加 Telegram 访问需要知情同意，清楚解释 agent 通过该渠道可以和不能做什么。

**3. Audited skill marketplace.** Every community contribution goes through automated security scanning and human review. Hidden prompt injections get caught before they reach users. Think Chrome Web Store review, not npm circa 2018.
**3. 审核的 skill 市场。** 每个社区贡献都经过自动安全扫描和人工审查。隐藏的提示注入在接触用户之前被捕获。想想 Chrome Web Store 审查，而不是 2018 年的 npm。

**4. Minimal permissions by default.** Agents start with zero access and opt into each capability. The principle of least privilege, applied to agent architecture.
**4. 默认最小权限。** Agent 从零访问开始并选择加入每个能力。最少特权原则，应用于 agent 架构。

**5. Transparent audit logging.** Users can see exactly what their agent did, what instructions it received, and what data it accessed. Not buried in log files — in a clear, searchable interface.
**5. 透明的审计日志。** 用户可以准确看到他们的 agent 做了什么、收到了什么指令、访问了什么数据。不是埋在日志文件中——在清晰、可搜索的界面中。

**6. Incident response.** When (not if) a security issue occurs, the provider has a process: detection, containment, notification, remediation. Not "check the Discord for updates."
**6. 事件响应。** 当（不是如果）发生安全问题时，提供商有一个流程：检测、containd、通知、修复。不是"查看 Discord 获取更新。"

OpenClaw could evolve into this. The foundation is there. The community is engaged. The team is building at the frontier of what's possible. But it requires a fundamental shift from "maximize flexibility and integrations" to "security by default." Those are different design philosophies, and right now, OpenClaw is firmly in the first camp.
OpenClaw 可以发展成这个。基础就在那里。社区很投入。团队正在建设的边缘。但是需要从"最大化灵活性和集成"到"默认安全"的根本转变。这是不同的设计哲学，而现在，OpenClaw 坚定地站在第一个阵营。

For technical users in the meantime: MiniClaw. One access point. Sandboxed. Headless. Boring. Secure.
对于目前的技术用户：MiniClaw。一个访问点。沙盒化。无头。无聊。安全。

For non-technical users: wait for the hosted, sandboxed versions. They're coming — the market demand is too obvious for them not to. Don't run autonomous agents on your personal machine with access to your accounts in the meantime. The convenience genuinely isn't worth the risk. Or if you do, understand what you're accepting.
对于非技术用户：等待托管、沙盒化的版本。它们即将到来——市场需求太明显了。在那之前，不要在您的个人机器上运行带有账户访问权限的自主 agent。便利真的不值得这个风险。或者，如果您这样做了，了解您接受什么。

I want to be honest about the counter-argument here, because it's not trivial. For non-technical users who genuinely need AI automation, the alternative I'm describing — headless servers, SSH, tmux — is inaccessible. Telling a marketing manager to "just SSH into a Mac Mini" isn't a solution. It's a dismissal. The right answer for non-technical users is not "don't use recursive agents." It's "use them in a sandboxed, hosted, professionally managed environment where someone else's job is to handle security." You pay a subscription fee. In return, you get peace of mind. That model is coming. Until it arrives, the risk calculus on self-hosted multi-channel agents is heavily skewed toward "not worth it."
我想在这里对反驳诚实地说明，因为这不是微不足道的。对于真正需要 AI 自动化的非技术用户，我描述的替代方案——无头服务器、SSH、tmux——是不可访问的。告诉营销经理"只需 SSH 到 Mac Mini"不是解决方案。这是一个 dismissal。非技术用户的正确答案不是"不要使用递归 agent。""在沙盒化、托管、专业管理的环境中运行它，在那里别人的工作是处理安全。"您支付订阅费。作为回报，您获得安心。这个模式即将到来。在它到来之前，自托管多渠道 agent 的风险计算严重偏向"不值得"。

> 📸 **[DIAGRAM: "The Winning Architecture" — a layered stack showing: Hosted Infrastructure (bottom) -> Sandboxed Containers (middle) -> Audited Skills + Minimal Permissions (upper) -> Clean Dashboard (top). Each layer labeled with its security property. Contrast with OpenClaw's flat architecture where everything runs on the user's machine.]**
> 📸 **[DIAGRAM: "The Winning Architecture" — a layered stack showing: Hosted Infrastructure (bottom) -> Sandboxed Containers (middle) -> Audited Skills + Minimal Permissions (upper) -> Clean Dashboard (top). Each layer labeled with its security property. Contrast with OpenClaw's flat architecture where everything runs on the user's machine.]**
> *What the winning recursive agent architecture looks like.*
> *获胜的递归 agent 架构是什么样的。*

---

## What You Should Do Right Now
## 您现在应该做什么

If you're currently running OpenClaw or considering it, here's the practical takeaway.
如果您目前正在运行 OpenClaw 或正在考虑它，这里是实用的要点。

### If you're running OpenClaw today:
### 如果您今天正在运行 OpenClaw：

1. **Audit every ClawdHub skill you've installed.** Read the full source, not just the visible description. Look for hidden instructions below the task definition. If you can't read the source and understand what it does, remove it.
1. **审计您安装的每个 ClawdHub skill。** 阅读完整源代码，而不仅仅是可见描述。在任务定义下方寻找隐藏的指令。如果您无法阅读源代码并理解它的作用，请删除它。

2. **Review your channel permissions.** For each connected channel (Telegram, Discord, X, email), ask: "If this channel is compromised, what can the attacker access through my agent?" If the answer is "everything else I've connected," you have a blast radius problem.
2. **审查您的渠道权限。** 对于每个连接的渠道（Telegram, Discord, X, email），问一下："如果这个渠道被破坏，攻击者可以通过我的 agent 访问什么？"如果答案是"我连接的所有其他东西"，您就有爆炸半径问题。

3. **Isolate your agent's execution environment.** If your agent runs on the same machine as your personal accounts, iMessage, email client, and browser with saved passwords — that's the maximum possible blast radius. Consider running it in a container or on a dedicated machine.
3. **隔离您的 agent 的执行环境。** 如果您的 agent 与您的个人账户、iMessage、保存密码的浏览器运行在同一个设备上——那是最大可能的爆炸半径。考虑在容器或专用机器上运行它。

4. **Disable channels you don't actively need.** Every integration you have enabled that you're not using daily is attack surface you're paying for with no benefit. Trim it.
4. **禁用您不积极需要的渠道。** 您启用但每天不使用的每个集成都是您付费但没有好处的攻击 surface。精简它。

5. **Update to the latest version.** CVE-2026-25253 was patched in 2026.1.29. If you're running an older version, you have a known one-click RCE vulnerability. Update now.
5. **更新到最新版本。** CVE-2026-25253 在 2026.1.29 中修复。如果您正在运行旧版本，您有一个已知的一键 RCE 漏洞。现在更新。

### If you're considering OpenClaw:
### 如果您正在考虑 OpenClaw：

Ask yourself honestly: do you need multi-channel orchestration, or do you need an AI agent that can execute tasks? Those are different things. The agent functionality is available through Claude Code, Cursor, Codex, and other harnesses — without the multi-channel attack surface.
诚实地问一下自己：您需要多渠道编排，还是需要一个可以执行任务的 AI agent？这是不同的事情。agent 功能可通过 Claude Code、Cursor、Codex 和其他 harnesses 获得——没有多渠道攻击 surface。

If you decide the multi-channel orchestration is genuinely necessary for your workflow, go in with your eyes open. Know what you're connecting. Know what a compromised channel means. Read every skill before you install it. Run it on a dedicated machine, not your personal laptop.
如果您决定多渠道编排对您的工作流程确实必要，那么睁开您的眼睛去了解。知道您连接了什么。知道一个被破坏的渠道意味着什么。在安装每个 skill 之前阅读它。在专用机器上运行它，而不是您的个人笔记本电脑。

### If you're building in this space:
### 如果您在这个领域构建：

The biggest opportunity isn't more features or more integrations. It's building the version that's secure by default. The team that nails hosted, sandboxed, audited recursive agents for consumers and enterprises will own this market. Right now, that product doesn't exist yet.
最大的机会不是更多功能或更多集成。这是构建默认安全的版本。团队为消费者和企业构建托管、沙盒化、审核的递归 agent 将拥有这个市场。现在，那个产品还不存在。

The playbook is clear: hosted infrastructure so users don't manage servers, sandboxed execution so compromise is contained, an audited skill marketplace so supply chain attacks get caught before they reach users, and transparent logging so everyone can see what their agent is doing. This is all solvable with known technology. The question is whether anyone prioritizes it over growth speed.
剧本很明确：托管基础设施，这样用户不管理服务器，沙盒化执行，这样危害被containd，审核的 skill 市场，这样供应链攻击在被用户接触之前被捕获，透明的日志，这样每个人都可以看到他们的 agent 在做什么。这都是用已知技术解决的。问题是是否有人优先于增长速度。

> 📸 **[CHECKLIST GRAPHIC: The 5-point "If you're running OpenClaw today" list rendered as a visual checklist with checkboxes, designed for sharing.]**
> 📸 **[CHECKLIST GRAPHIC: The 5-point "If you're running OpenClaw today" list rendered as a visual checklist with checkboxes, designed for sharing.]**
> *The minimum security checklist for current OpenClaw users.*
> *当前 OpenClaw 用户的最低安全检查清单。*

---

## Closing
## 结尾

This article isn't an attack on OpenClaw. I want to be clear about that.
这篇文章不是对 OpenClaw 的攻击。我想清楚地说明这一点。

The team is building something ambitious. The community is passionate. The vision of recursive agents managing our digital lives is probably correct as a long-term prediction. I spent a week using it because I genuinely wanted it to work.
团队正在构建一些雄心勃勃的东西。社区很投入。递归 agent 管理我们数字生活的愿景可能作为长期预测是正确的。我使用它过了一周，因为我真的希望它能工作。

But the security model isn't ready for the adoption it's getting. And the people flooding in — especially the non-technical users who are most excited — don't know what they don't know.
但是安全模型还没有为它正在获得的采用做好准备。而且涌入的人——特别是最兴奋的非技术用户——不知道他们不知道的。

When Andrej Karpathy calls something a "dumpster fire" and explicitly recommends against running it on your computer. When CrowdStrike calls it a "full-scale breach enabler." When Palo Alto Networks identifies a "lethal trifecta" baked into the architecture. When 20% of the skill marketplace is actively malicious. When a single CVE exposes 42,665 instances with 93.4% having authentication bypass conditions.
当 Andrej Karpathy 称某物为"垃圾堆火灾"并明确建议不要在您的计算机上运行它时。当 CrowdStrike 称其为"全面违规推动者"时。当 Palo Alto Networks 识别出架构中内置的"致命三位一体"时。当 20% 的 skill 市场是主动恶意的时。当单个 CVE 暴露 42,665 个实例，93.4% 有身份验证绕过条件时。

At some point, you have to take the evidence seriously.
在某种程度上，您必须认真对待证据。

I built AgentShield partly because of what I found during that week with OpenClaw. If you want to scan your own agent setup for the kinds of vulnerabilities I've described here — hidden prompt injections in skills, overly broad permissions, unsandboxed execution environments — AgentShield can help with that assessment. But the bigger point isn't any particular tool.
我构建 AgentShield 部分是因为我在 OpenClaw 的那一周中发现的内容。如果您想扫描您自己的 agent 设置，查找我在这里描述的漏洞类型——skills 中隐藏的提示注入、过于广泛的权限、不沙盒化的执行环境——AgentShield 可以帮助进行评估。但更大的要点不是任何特定的工具。

The bigger point is: **security has to be a first-class constraint in agent infrastructure, not an afterthought.**
更大的要点是：**安全必须是 agent 基础设施中的第一类约束，而不是事后想法。**

The industry is building the plumbing for autonomous AI. These are the systems that will manage people's email, their finances, their communications, their business operations. If we get the security wrong at the foundation layer, we will be paying for it for decades. Every compromised agent, every leaked credential, every deleted inbox — these aren't just individual incidents. They're erosion of the trust that the entire AI agent ecosystem needs to survive.
该行业正在为我们构建自主 AI 的管道。这些是将管理人们的电子邮件、财务、通信、业务运营的系统。如果我们在基础层搞错了安全，我们将为此付出代价数十年。每个被破坏的 agent、每个泄露的凭据、每个被删除的收件箱——这些不仅仅是单独的事件。它们是整个 AI agent 生态系统需要生存的信任的侵蚀。

The people building in this space have a responsibility to get this right. Not eventually. Not in the next version. Now.
在这个领域构建的人有责任正确地做到这一点。不是最终。不是在下一个版本。现在。

I'm optimistic about where this is heading. The demand for secure, autonomous agents is obvious. The technology to build them correctly exists. Someone is going to put the pieces together — hosted infrastructure, sandboxed execution, audited skills, transparent logging — and build the version that works for everyone. That's the product I want to use. That's the product I think wins.
我对这个方向感到乐观。对安全、自主 agent 的需求是显而易见的。正确构建它们的技术存在。有人会把碎片整合在一起——托管基础设施、沙盒化执行、审核的技能，透明日志——并构建适合每个人的版本。这是我想使用的产品。这是我认为会赢的产品。

Until then: read the source. Audit your skills. Minimize your attack surface. And when someone tells you that connecting seven channels to an autonomous agent with root access is a feature, ask them who's securing the doors.
在此之前：阅读源代码。审计您的 skills。最小化您的攻击表面。当有人告诉您将七个渠道连接到具有 root 访问权限的自主 agent 是一个功能时，询问他们谁在保护门。

Build secure by design. Not secure by accident.
默认构建安全。不是意外安全。

**What do you think? Am I being too cautious, or is the community moving too fast?** I genuinely want to hear the counter-arguments. Reply or DM me on X.
**您怎么看？我太谨慎了，还是社区发展得太快了？** 我真的想听到反驳。在 X 上回复或 DM 我。

---

## references
## 参考资料

- [OWASP Top 10 for Agentic Applications (2026)](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/) — Palo Alto mapped OpenClaw to every category
- [OWASP Top 10 for Agentic Applications (2026)](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/) — Palo Alto 将 OpenClaw 映射到每个类别
- [CrowdStrike: What Security Teams Need to Know About OpenClaw](https://www.crowdstrike.com/en-us/blog/what-security-teams-need-to-know-about-openclaw-ai-super-agent/)
- [CrowdStrike: What Security Teams Need to Know About OpenClaw](https://www.crowdstrike.com/en-us/blog/what-security-teams-need-to-know-about-openclaw-ai-super-agent/)
- [Palo Alto Networks: Why Moltbot May Signal AI Crisis](https://www.paloaltonetworks.com/blog/network-security/why-moltbot-may-signal-ai-crisis/) — The "lethal trifecta" + memory poisoning
- [Palo Alto Networks: Why Moltbot May Signal AI Crisis](https://www.paloaltonetworks.com/blog/network-security/why-moltbot-may-signal-ai-crisis/) — The "lethal trifecta" + memory poisoning
- [Kaspersky: New OpenClaw AI Agent Found Unsafe for Use](https://www.kaspersky.com/blog/openclaw-vulnerabilities-exposed/55263/)
- [Kaspersky: New OpenClaw AI Agent Found Unsafe for Use](https://www.kaspersky.com/blog/openclaw-vulnerabilities-exposed/55263/)
- [Wiz: Hacking Moltbook — 1.5M API Keys Exposed](https://www.wiz.io/blog/exposed-moltbook-database-reveals-millions-of-api-keys)
- [Wiz: Hacking Moltbook — 1.5M API Keys Exposed](https://www.wiz.io/blog/exposed-moltbook-database-reveals-millions-of-api-keys)
- [Trend Micro: Malicious OpenClaw Skills Distribute Atomic macOS Stealer](https://www.trendmicro.com/en_us/research/26/b/openclaw-skills-used-to-distribute-atomic-macos-stealer.html)
- [Trend Micro: Malicious OpenClaw Skills Distribute Atomic macOS Stealer](https://www.trendmicro.com/en_us/research/26/b/openclaw-skills-used-to-distribute-atomic-macos-stealer.html)
- [Adversa AI: OpenClaw Security Guide 2026](https://adversa.ai/blog/openclaw-security-101-vulnerabilities-hardening-2026/)
- [Adversa AI: OpenClaw Security Guide 2026](https://adversa.ai/blog/openclaw-security-101-vulnerabilities-hardening-2026/)
- [Cisco: Personal AI Agents Like OpenClaw Are a Security Nightmare](https://blogs.cisco.com/ai/personal-ai-agents-like-openclaw-are-a-security-nightmare)
- [Cisco: Personal AI Agents Like OpenClaw Are a Security Nightmare](https://blogs.cisco.com/ai/personal-ai-agents-like-openclaw-are-a-security-nightmare)
- [The Shorthand Guide to Securing Your Agent](./the-security-guide.md) — Practical defense guide
- [The Shorthand Guide to Securing Your Agent](./the-security-guide.md) — 实用防御指南
- [AgentShield on npm](https://www.npmjs.com/package/ecc-agentshield) — Zero-install agent security scanning
- [AgentShield on npm](https://www.npmjs.com/package/ecc-agentshield) — 零安装 agent 安全扫描

> **Series navigation:**
> **系列导航：**
> - Part 1: [The Shorthand Guide to Everything Claude Code](./the-shortform-guide.md) — Setup and configuration
> - 第 1 部分：[Everything Claude Code 简明指南](./the-shortform-guide.md) — 设置和配置
> - Part 2: [The Longform Guide to Everything Claude Code](./the-longform-guide.md) — Advanced patterns and workflows
> - 第 2 部分：[Everything Claude Code 详细指南](./the-longform-guide.md) — 高级模式和工作流
> - Part 3: The Hidden Danger of OpenClaw (this article) — Security lessons from the agent frontier
> - 第 3 部分：OpenClaw 的隐藏危险（本文）— Agent 前沿的安全教训
> - Part 4: [The Shorthand Guide to Securing Your Agent](./the-security-guide.md) — Practical agent security
> - 第 4 部分：[Agent 安全简明指南](./the-security-guide.md) — 实用 agent 安全

---

*Affaan Mustafa ([@affaanmustafa](https://x.com/affaanmustafa)) builds AI coding tools and writes about AI infrastructure security. His everything-claude-code repo has 50K+ GitHub stars. He created AgentShield and won the Anthropic x Forum Ventures hackathon building [zenith.chat](https://zenith.chat).*
*Affaan Mustafa ([@affaanmustafa](https://x.com/affaanmustafa)) 构建 AI 编码工具并撰写 AI 基础设施安全。他的 everything-claude-code 仓库有 50K+ GitHub stars。他创建了 AgentShield，并在 Anthropic x Forum Ventures hackathon 中获胜，构建了 [zenith.chat](https://zenith.chat)。*
