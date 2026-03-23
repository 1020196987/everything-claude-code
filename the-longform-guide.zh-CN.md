# The Longform Guide to Everything Claude Code
# Everything Claude Code 详细指南

![Header: The Longform Guide to Everything Claude Code](./assets/images/longform/01-header.png)

---

> **Prerequisite**: This guide builds on [The Shorthand Guide to Everything Claude Code](./the-shortform-guide.md). Read that first if you haven't set up skills, hooks, subagents, MCPs, and plugins.
> **先修要求**: 本指南基于 [Everything Claude Code 简明指南](./the-shortform-guide.md)。如果您还没有设置 skills、hooks、子代理、MCPs 和插件，请先阅读该指南。

![Reference to Shorthand Guide](./assets/images/longform/02-shortform-reference.png)
*The Shorthand Guide - read it first*
*The Shorthand Guide - 请先阅读*

In the shorthand guide, I covered the foundational setup: skills and commands, hooks, subagents, MCPs, plugins, and the configuration patterns that form the backbone of an effective Claude Code workflow. That was the setup guide and the base infrastructure.
在简明指南中，我涵盖了基础设置：skills 和 commands、hooks、子代理、MCPs、插件，以及构成有效 Claude Code 工作流骨干的配置模式。那是设置指南和基础架构。

This longform guide goes into the techniques that separate productive sessions from wasteful ones. If you haven't read the shorthand guide, go back and set up your configs first. What follows assumes you have skills, agents, hooks, and MCPs already configured and working.
本详细指南深入探讨了将高效会话与浪费会话区分开来的技术。如果您还没有阅读简明指南，请返回并先设置您的配置。以下内容假设您已经配置好并运行了 skills、agents、hooks 和 MCPs。

The themes here: token economics, memory persistence, verification patterns, parallelization strategies, and the compound effects of building reusable workflows. These are the patterns I've refined over 10+ months of daily use that make the difference between being plagued by context rot within the first hour, versus maintaining productive sessions for hours.
这里的主题：token 经济性、内存持久化、验证模式、并行化策略，以及构建可重用工作流的复合效果。这些是我在 10 多个月的日常使用中精炼出的模式，这些模式决定了是在第一个小时内被上下文腐烂困扰，还是保持数小时的高效会话。

Everything covered in the shorthand and longform guides is available on GitHub: `github.com/affaan-m/everything-claude-code`
简明指南和详细指南中涵盖的所有内容都可以在 GitHub 上找到：`github.com/affaan-m/everything-claude-code`

---

## Tips and Tricks
## 技巧和窍门

### Some MCPs are Replaceable and Will Free Up Your Context Window
### 某些 MCP 可以替换，会释放您的上下文窗口

For MCPs such as version control (GitHub), databases (Supabase), deployment (Vercel, Railway) etc. - most of these platforms already have robust CLIs that the MCP is essentially just wrapping. The MCP is a nice wrapper but it comes at a cost.
对于 MCP 如版本控制（GitHub）、数据库（Supabase）、部署（Vercel, Railway）等——这些平台大多数已经有强大的 CLI，MCP 本质上只是在包装它们。MCP 是一个不错的包装，但有代价。

To have the CLI function more like an MCP without actually using the MCP (and the decreased context window that comes with it), consider bundling the functionality into skills and commands. Strip out the tools the MCP exposes that make things easy and turn those into commands.
为了让 CLI 功能更像 MCP 而实际上不使用 MCP（以及随之减少的上下文窗口），考虑将功能捆绑到 skills 和 commands 中。剥离 MCP 暴露的使事情变得简单的工具，并将它们转化为 commands。

Example: instead of having the GitHub MCP loaded at all times, create a `/gh-pr` command that wraps `gh pr create` with your preferred options. Instead of the Supabase MCP eating context, create skills that use the Supabase CLI directly.
示例：不要始终加载 GitHub MCP，而是创建一个包装了 `gh pr create` 且带有您首选选项的 `/gh-pr` command。不要让 Supabase MCP 消耗上下文，创建直接使用 Supabase CLI 的 skills。

With lazy loading, the context window issue is mostly solved. But token usage and cost is not solved in the same way. The CLI + skills approach is still a token optimization method.
通过延迟加载，上下文窗口问题基本得到解决。但 token 使用和成本并没有以同样的方式解决。CLI + skills 方法仍然是一种 token 优化方法。

---

## IMPORTANT STUFF
## 重要内容

### Context and Memory Management
### 上下文和内存管理

For sharing memory across sessions, a skill or command that summarizes and checks in on progress then saves to a `.tmp` file in your `.claude` folder and appends to it until the end of your session is the best bet. The next day it can use that as context and pick up where you left off, create a new file for each session so you don't pollute old context into new work.
为了跨会话共享内存，一个 skill 或 command 会总结并检查进度，然后保存到您的 `.claude` 文件夹中的 `.tmp` 文件，并在会话期间不断追加到它。最好第二天可以使用它作为上下文并从您停止的地方继续，为每个会话创建一个新文件，这样您就不会将旧的上下文污染到新工作中。

![Session Storage File Tree](./assets/images/longform/03-session-storage.png)
*Example of session storage -> <https://github.com/affaan-m/everything-claude-code/tree/main/examples/sessions>*
*会话存储示例 -> <https://github.com/affaan-m/everything-claude-code/tree/main/examples/sessions>*

Claude creates a file summarizing current state. Review it, ask for edits if needed, then start fresh. For the new conversation, just provide the file path. Particularly useful when you're hitting context limits and need to continue complex work. These files should contain:
Claude 创建一个总结当前状态的文件。检查它，如有需要请求编辑，然后重新开始。对于新对话，只需提供文件路径。当您达到上下文限制并需要继续复杂工作时，这特别有用。这些文件应包含：

- What approaches worked (verifiably with evidence)
- 什么方法有效（可验证地有证据）
- Which approaches were attempted but did not work
- 尝试了哪些但不工作的方法
- Which approaches have not been attempted and what's left to do
- 哪些方法尚未尝试以及还有什么要做

**Clearing Context Strategically:**
**战略性地清除上下文：**

Once you have your plan set and context cleared (default option in plan mode in Claude Code now), you can work from the plan. This is useful when you've accumulated a lot of exploration context that's no longer relevant to execution. For strategic compacting, disable auto compact. Manually compact at logical intervals or create a skill that does so for you.
一旦您设置好计划并清除了上下文（Claude Code 现在计划模式中的默认选项），您可以从计划开始。当您积累了大量不再与执行相关的探索上下文时，这很有用。对于战略性压缩，禁用自动压缩。在逻辑间隔手动压缩或创建一个为您执行此操作的 skill。

**Advanced: Dynamic System Prompt Injection**
**高级：动态系统提示注入**

One pattern I picked up: instead of solely putting everything in CLAUDE.md (user scope) or `.claude/rules/` (project scope) which loads every session, use CLI flags to inject context dynamically.
我采用的一种模式：不要把所有东西都放在 CLAUDE.md（用户范围）或 `.claude/rules/`（项目范围）中，这些在每个会话加载，而是使用 CLI 标志动态注入上下文。

```bash
claude --system-prompt "$(cat memory.md)"
```
```bash
claude --system-prompt "$(cat memory.md)"
```

This lets you be more surgical about what context loads when. System prompt content has higher authority than user messages, which have higher authority than tool results.
这让您对加载什么上下文更加精确。系统提示内容比用户消息具有更高的权威性，用户消息比工具结果具有更高的权威性。

**Practical setup:**
**实用设置：**

```bash
# Daily development
alias claude-dev='claude --system-prompt "$(cat ~/.claude/contexts/dev.md)"'
# 日常开发
alias claude-dev='claude --system-prompt "$(cat ~/.claude/contexts/dev.md)"'

# PR review mode
alias claude-review='claude --system-prompt "$(cat ~/.claude/contexts/review.md)"'
# PR 审查模式
alias claude-review='claude --system-prompt "$(cat ~/.claude/contexts/review.md)"'

# Research/exploration mode
alias claude-research='claude --system-prompt "$(cat ~/.claude/contexts/research.md)"'
# 研究/探索模式
alias claude-research='claude --system-prompt "$(cat ~/.claude/contexts/research.md)"'
```

**Advanced: Memory Persistence Hooks**
**高级：内存持久化 Hooks**

There are hooks most people don't know about that help with memory:
大多数人不了解的帮助内存的 hooks：

- **PreCompact Hook**: Before context compaction happens, save important state to a file
  **PreCompact Hook**: 在上下文压缩发生前，将重要状态保存到文件
- **Stop Hook (Session End)**: On session end, persist learnings to a file
  **Stop Hook (会话结束)**: 会话结束时，将学习内容持久化到文件
- **SessionStart Hook**: On new session, load previous context automatically
  **SessionStart Hook**: 新会话时，自动加载之前的上下文

I've built these hooks and they're in the repo at `github.com/affaan-m/everything-claude-code/tree/main/hooks/memory-persistence`
我已构建了这些 hooks，它们在 `github.com/affaan-m/everything-claude-code/tree/main/hooks/memory-persistence`

---

### Continuous Learning / Memory
### 持续学习/内存

If you've had to repeat a prompt multiple times and Claude ran into the same problem or gave you a response you've heard before - those patterns must be appended to skills.
如果您不得不多次重复一个提示，而 Claude 遇到了相同的问题或给出了您之前听过的响应——这些模式必须追加到 skills 中。

**The Problem:** Wasted tokens, wasted context, wasted time.
**问题：** 浪费的 token，浪费的上下文，浪费的时间。

**The Solution:** When Claude Code discovers something that isn't trivial - a debugging technique, a workaround, some project-specific pattern - it saves that knowledge as a new skill. Next time a similar problem comes up, the skill gets loaded automatically.
**解决方案：** 当 Claude Code 发现一些非平凡的东西时——调试技术、解决方法、一些特定于项目的模式——它将该知识保存为新的 skill。下次出现类似问题时，该 skill 会自动加载。

I've built a continuous learning skill that does this: `github.com/affaan-m/everything-claude-code/tree/main/skills/continuous-learning`
我构建了一个执行此操作的持续学习 skill：`github.com/affaan-m/everything-claude-code/tree/main/skills/continuous-learning`

**Why Stop Hook (Not UserPromptSubmit):**
**为什么用 Stop Hook（不是 UserPromptSubmit）：**

The key design decision is using a **Stop hook** instead of UserPromptSubmit. UserPromptSubmit runs on every single message - adds latency to every prompt. Stop runs once at session end - lightweight, doesn't slow you down during the session.
关键设计决策是使用 **Stop hook** 而不是 UserPromptSubmit。UserPromptSubmit 在每条消息上运行——给每个提示增加延迟。Stop 在会话结束时运行一次——轻量级，不会话期间减慢您的速度。

---

### Token Optimization
### Token 优化

**Primary Strategy: Subagent Architecture**
**主要策略：子代理架构**

Optimize the tools you use and subagent architecture designed to delegate the cheapest possible model that is sufficient for the task.
优化您使用的工具和子代理架构，委托给足够完成任务的尽可能便宜的模型。

**Model Selection Quick Reference:**
**模型选择快速参考：**

![Model Selection Table](./assets/images/longform/04-model-selection.png)
*Hypothetical setup of subagents on various common tasks and reasoning behind the choices*
*各种常见任务的子代理假设设置及选择原因*

| Task Type                 | Model  | Why                                        |
| ------------------------- | ------ | ------------------------------------------ |
| Exploration/search        | Haiku  | Fast, cheap, good enough for finding files |
| Simple edits              | Haiku  | Single-file changes, clear instructions    |
| Multi-file implementation | Sonnet | Best balance for coding                    |
| Complex architecture      | Opus   | Deep reasoning needed                      |
| PR reviews                | Sonnet | Understands context, catches nuance        |
| Security analysis         | Opus   | Can't afford to miss vulnerabilities       |
| Writing docs              | Haiku  | Structure is simple                        |
| Debugging complex bugs    | Opus   | Needs to hold entire system in mind        |
| 任务类型                 | 模型  | 原因                                        |
| ------------------------- | ------ | ------------------------------------------ |
| 探索/搜索                 | Haiku  | 快速、便宜、足以找到文件 |
| 简单编辑                 | Haiku  | 单文件更改、清晰指令 |
| 多文件实现               | Sonnet | 编码最佳平衡 |
| 复杂架构                 | Opus   | 需要深度推理 |
| PR 审查                   | Sonnet | 理解上下文、捕捉细微差别 |
| 安全分析                 | Opus   | 不能遗漏漏洞 |
| 写文档                   | Haiku  | 结构简单 |
| 调试复杂 bug             | Opus   | 需要将整个系统记在脑中 |

Default to Sonnet for 90% of coding tasks. Upgrade to Opus when first attempt failed, task spans 5+ files, architectural decisions, or security-critical code.
90% 的编码任务默认使用 Sonnet。升级到 Opus 当首次尝试失败、任务跨越 5+ 个文件、架构决策、或安全关键代码时。

**Pricing Reference:**
**定价参考：**

![Claude Model Pricing](./assets/images/longform/05-pricing-table.png)
*Source: <https://platform.claude.com/docs/en/about-claude/pricing>*
*来源: <https://platform.claude.com/docs/en/about-claude/pricing>*

**Tool-Specific Optimizations:**
**工具特定优化：**

Replace grep with mgrep - ~50% token reduction on average compared to traditional grep or ripgrep:
用 mgrep 替换 grep——与传统 grep 或 ripgrep 相比，平均减少约 50% 的 token：

![mgrep Benchmark](./assets/images/longform/06-mgrep-benchmark.png)
*In our 50-task benchmark, mgrep + Claude Code used ~2x fewer tokens than grep-based workflows at similar or better judged quality. Source: mgrep by @mixedbread-ai*
*在我们的 50 任务基准测试中，mgrep + Claude Code 使用的 token 约为基于 grep 的工作流的一半，而质量判断相似或更好。来源：@mixedbread-ai 的 mgrep*

**Modular Codebase Benefits:**
**模块化代码库的好处：**

Having a more modular codebase with main files being in the hundreds of lines instead of thousands of lines helps both in token optimization costs and getting a task done right on the first try.
拥有更模块化的代码库，主要文件几百行而不是几千行，既有助于 token 优化成本，也有助于在第一次尝试正确完成任务。

---

### Verification Loops and Evals
### 验证循环和 Evals

**Benchmarking Workflow:**
**基准测试工作流：**

Compare asking for the same thing with and without a skill and checking the output difference:
有 skill 和没有 skill 请求相同内容并检查输出差异：

Fork the conversation, initiate a new worktree in one of them without the skill, pull up a diff at the end, see what was logged.
分叉对话，在其中一个没有 skill 的情况下启动新 worktree，最后拉取差异，看看记录了什么。

**Eval Pattern Types:**
**Eval 模式类型：**

- **Checkpoint-Based Evals**: Set explicit checkpoints, verify against defined criteria, fix before proceeding
  **基于 Checkpoint 的 Evals**: 设置明确的检查点，根据定义的标准验证，修复后再继续
- **Continuous Evals**: Run every N minutes or after major changes, full test suite + lint
  **持续 Evals**: 每 N 分钟或重大更改后运行，完整测试套件 + lint

**Key Metrics:**
**关键指标：**

```
pass@k: At least ONE of k attempts succeeds
        k=1: 70%  k=3: 91%  k=5: 97%

pass^k: ALL k attempts must succeed
        k=1: 70%  k=3: 34%  k=5: 17%
```
```
pass@k: k 次尝试中至少一次成功
        k=1: 70%  k=3: 91%  k=5: 97%

pass^k: k 次尝试必须全部成功
        k=1: 70%  k=3: 34%  k=5: 17%
```

Use **pass@k** when you just need it to work. Use **pass^k** when consistency is essential.
当您只需要它工作时使用 **pass@k**。当一致性至关重要时使用 **pass^k**。

---

## PARALLELIZATION
## 并行化

When forking conversations in a multi-Claude terminal setup, make sure the scope is well-defined for the actions in the fork and the original conversation. Aim for minimal overlap when it comes to code changes.
在多 Claude 终端设置中分叉对话时，确保分叉中的操作和原始对话的范围定义良好。在代码更改方面尽量减少重叠。

**My Preferred Pattern:**
**我的首选模式：**

Main chat for code changes, forks for questions about the codebase and its current state, or research on external services.
主聊天用于代码更改，分叉用于关于代码库及其当前状态的问题，或外部服务的研究。

**On Arbitrary Terminal Counts:**
**关于任意终端数量：**

![Boris on Parallel Terminals](./assets/images/longform/07-boris-parallel.png)
*Boris (Anthropic) on running multiple Claude instances*
*Boris (Anthropic) 关于运行多个 Claude 实例*

Boris has tips on parallelization. He's suggested things like running 5 Claude instances locally and 5 upstream. I advise against setting arbitrary terminal amounts. The addition of a terminal should be out of true necessity.
Boris 有一些关于并行化的建议。他建议过在本地运行 5 个 Claude 实例和 5 个上游实例。我建议不要设置任意的终端数量。终端的增加应该是出于真正的必要性。

Your goal should be: **how much can you get done with the minimum viable amount of parallelization.**
您的目标应该是：**您能用最小可行的并行化完成多少工作。**

**Git Worktrees for Parallel Instances:**
**用于并行实例的 Git Worktrees：**

```bash
# Create worktrees for parallel work
git worktree add ../project-feature-a feature-a
git worktree add ../project-feature-b feature-b
git worktree add ../project-refactor refactor-branch
# 为并行工作创建 worktrees
git worktree add ../project-feature-a feature-a
git worktree add ../project-feature-b feature-b
git worktree add ../project-refactor refactor-branch

# Each worktree gets its own Claude instance
cd ../project-feature-a && claude
# 每个 worktree 有自己的 Claude 实例
cd ../project-feature-a && claude
```

IF you are to begin scaling your instances AND you have multiple instances of Claude working on code that overlaps with one another, it's imperative you use git worktrees and have a very well-defined plan for each. Use `/rename <name here>` to name all your chats.
如果您开始扩展您的实例，并且您有多个 Claude 实例处理相互重叠的代码，**您必须使用 git worktrees**，并为每个实例制定非常明确的计划。使用 `/rename <name here>` 为您所有的聊天命名。

![Two Terminal Setup](./assets/images/longform/08-two-terminals.png)
*Starting Setup: Left Terminal for Coding, Right Terminal for Questions - use /rename and /fork*
*初始设置：左终端用于编码，右终端用于问题 - 使用 /rename 和 /fork*

**The Cascade Method:**
**级联方法：**

When running multiple Claude Code instances, organize with a "cascade" pattern:
当运行多个 Claude Code 实例时，使用"级联"模式组织：

- Open new tasks in new tabs to the right
- 在新标签页中从右向左打开新任务
- Sweep left to right, oldest to newest
- 从左到右，从最旧到最新
- Focus on at most 3-4 tasks at a time
- 一次专注于最多 3-4 个任务

---

## GROUNDWORK
## 基础工作

**The Two-Instance Kickoff Pattern:**
**双实例启动模式：**

For my own workflow management, I like to start an empty repo with 2 open Claude instances.
对于我自己的工作流管理，我喜欢用 2 个打开的 Claude 实例启动一个空仓库。

**Instance 1: Scaffolding Agent**
**实例 1: 脚手架 Agent**
- Lays down the scaffold and groundwork
- 铺设脚手架和基础工作
- Creates project structure
- 创建项目结构
- Sets up configs (CLAUDE.md, rules, agents)
- 设置配置（CLAUDE.md、rules、agents）

**Instance 2: Deep Research Agent**
**实例 2: 深度研究 Agent**
- Connects to all your services, web search
- 连接您所有的服务、网络搜索
- Creates the detailed PRD
- 创建详细的 PRD
- Creates architecture mermaid diagrams
- 创建架构 mermaid 图
- Compiles the references with actual documentation clips
- 编译带有实际文档片段的参考资料

**llms.txt Pattern:**
**llms.txt 模式：**

If available, you can find an `llms.txt` on many documentation references by doing `/llms.txt` on them once you reach their docs page. This gives you a clean, LLM-optimized version of the documentation.
如果可用，您可以通过在文档页面上执行 `/llms.txt` 在许多文档参考资料上找到 `llms.txt`。这为您提供了文档的 LLM 优化版本。

**Philosophy: Build Reusable Patterns**
**哲学：构建可重用模式**

From @omarsar0: "Early on, I spent time building reusable workflows/patterns. Tedious to build, but this had a wild compounding effect as models and agent harnesses improved."
来自 @omarsar0："早期，我花时间构建可重用的工作流/模式。构建起来很繁琐，但随着模型和 agent harnesses 的改进，这产生了疯狂的复合效应。"

**What to invest in:**
**应该投资的内容：**

- Subagents
- Skills
- Commands
- Planning patterns
- MCP tools
- Context engineering patterns
- 子代理
- Skills
- Commands
- 规划模式
- MCP 工具
- 上下文工程模式

---

## Best Practices for Agents & Sub-Agents
## Agent 和子代理的最佳实践

**The Sub-Agent Context Problem:**
**子代理上下文问题：**

Sub-agents exist to save context by returning summaries instead of dumping everything. But the orchestrator has semantic context the sub-agent lacks. The sub-agent only knows the literal query, not the PURPOSE behind the request.
子代理存在是为了通过返回摘要而不是转储所有内容来节省上下文。但编排器具有子代理缺乏的语义上下文。子代理只知道字面查询，而不是请求背后的**目的**。

**Iterative Retrieval Pattern:**
**迭代检索模式：**

1. Orchestrator evaluates every sub-agent return
1. 编排器评估每个子代理返回
2. Ask follow-up questions before accepting it
2. 在接受之前提出后续问题
3. Sub-agent goes back to source, gets answers, returns
3. 子代理回到源，获取答案，然后返回
4. Loop until sufficient (max 3 cycles)
4. 循环直到足够（最多 3 个周期）

**Key:** Pass objective context, not just the query.
**关键：** 传递客观上下文，而不仅仅是查询。

**Orchestrator with Sequential Phases:**
**带顺序阶段的编排器：**

```markdown
Phase 1: RESEARCH (use Explore agent) → research-summary.md
Phase 2: PLAN (use planner agent) → plan.md
Phase 3: IMPLEMENT (use tdd-guide agent) → code changes
Phase 4: REVIEW (use code-reviewer agent) → review-comments.md
Phase 5: VERIFY (use build-error-resolver if needed) → done or loop back
```
```markdown
Phase 1: RESEARCH (use Explore agent) → research-summary.md
Phase 2: PLAN (use planner agent) → plan.md
Phase 3: IMPLEMENT (use tdd-guide agent) → code changes
Phase 4: REVIEW (use code-reviewer agent) → review-comments.md
Phase 5: VERIFY (use build-error-resolver if needed) → done or loop back
```

**Key rules:**
**关键规则：**

1. Each agent gets ONE clear input and produces ONE clear output
1. 每个 agent 获得一个清晰的输入并产生一个清晰的输出
2. Outputs become inputs for next phase
2. 输出成为下一阶段的输入
3. Never skip phases
3. 永不跳过阶段
4. Use `/clear` between agents
4. 在 agent 之间使用 `/clear`
5. Store intermediate outputs in files
5. 将中间输出存储在文件中

---

## FUN STUFF / NOT CRITICAL JUST FUN TIPS
## 有趣的内容/不重要，只是有趣的技巧

### Custom Status Line
### 自定义状态行

You can set it using `/statusline` - then Claude will say you don't have one but can set it up for you and ask what you want in it.
您可以使用 `/statusline` 设置它——然后 Claude 会告诉您没有，但可以为您设置，并询问您想要什么。

See also: ccstatusline (community project for custom Claude Code status lines)
另见：ccstatusline（用于自定义 Claude Code 状态行的社区项目）

### Voice Transcription
### 语音转录

Talk to Claude Code with your voice. Faster than typing for many people.
用您的语音与 Claude Code 交谈。对许多人来说比打字更快。

- superwhisper, MacWhisper on Mac
- Mac 上的 superwhisper、MacWhisper
- Even with transcription mistakes, Claude understands intent
- 即使有转录错误，Claude 也能理解意图

### Terminal Aliases
### 终端别名

```bash
alias c='claude'
alias gb='github'
alias co='code'
alias q='cd ~/Desktop/projects'
```
```bash
alias c='claude'
alias gb='github'
alias co='code'
alias q='cd ~/Desktop/projects'
```

---

## Milestone
## 里程碑

![25k+ GitHub Stars](./assets/images/longform/09-25k-stars.png)
*25,000+ GitHub stars in under a week*
*不到一周内获得 25,000+ GitHub stars*

---

## Resources
## 资源

**Agent Orchestration:**
**Agent 编排：**

- claude-flow — Community-built enterprise orchestration platform with 54+ specialized agents
- claude-flow — 社区构建的企业编排平台，拥有 54+ 专业 agents

**Self-Improving Memory:**
**自改进内存：**

- See `skills/continuous-learning/` in this repo
- 请参阅此仓库中的 `skills/continuous-learning/`
- rlancemartin.github.io/2025/12/01/claude_diary/ - Session reflection pattern
- rlancemartin.github.io/2025/12/01/claude_diary/ - 会话反思模式

**System Prompts Reference:**
**系统提示参考：**

- system-prompts-and-models-of-ai-tools — Community collection of AI system prompts (110k+ stars)
- system-prompts-and-models-of-ai-tools — AI 工具系统提示的社区集合（110k+ stars）

**Official:**
**官方：**

- Anthropic Academy: anthropic.skilljar.com
- Anthropic Academy: anthropic.skilljar.com

---

## References
## 参考资料

- [Anthropic: Demystifying evals for AI agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents)
- [YK: 32 Claude Code Tips](https://agenticcoding.substack.com/p/32-claude-code-tips-from-basics-to)
- [RLanceMartin: Session Reflection Pattern](https://rlancemartin.github.io/2025/12/01/claude_diary/)
- @PerceptualPeak: Sub-Agent Context Negotiation
- @menhguin: Agent Abstractions Tierlist
- @omarsar0: Compound Effects Philosophy
- [Anthropic: Demystifying evals for AI agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents)
- [YK: 32 Claude Code Tips](https://agenticcoding.substack.com/p/32-claude-code-tips-from-basics-to)
- [RLanceMartin: Session Reflection Pattern](https://rlancemartin.github.io/2025/12/01/claude_diary/)
- @PerceptualPeak: Sub-Agent Context Negotiation
- @menhguin: Agent Abstractions Tierlist
- @omarsar0: Compound Effects Philosophy

---

*Everything covered in both guides is available on GitHub at [everything-claude-code](https://github.com/affaan-m/everything-claude-code)*
*两个指南中涵盖的所有内容都可以在 GitHub 上的 [everything-claude-code](https://github.com/affaan-m/everything-claude-code) 中找到*
