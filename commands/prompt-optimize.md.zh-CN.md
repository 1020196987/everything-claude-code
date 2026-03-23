---
description: Analyze a draft prompt and output an optimized, ECC-enriched version ready to paste and run. Does NOT execute the task — outputs advisory analysis only.
description-zh: 分析草稿提示并输出优化后的、ECC 增强的版本，准备好粘贴和运行。不执行任务 — 仅输出咨询分析。
---

# /prompt-optimize

Analyze and optimize the following prompt for maximum ECC leverage.
分析和优化以下提示以获得最大的 ECC 优势。

## Your Task
## 你的任务

Apply the **prompt-optimizer** skill to the user's input below. Follow the 6-phase analysis pipeline:
将 **prompt-optimizer** 技能应用于用户下面的输入。遵循 6 阶段分析流程：

0. **Project Detection** — Read CLAUDE.md, detect tech stack from project files (package.json, go.mod, pyproject.toml, etc.)
0. **项目检测** — 读取 CLAUDE.md，从项目文件检测技术栈（package.json、go.mod、pyproject.toml 等）

1. **Intent Detection** — Classify the task type (new feature, bug fix, refactor, research, testing, review, documentation, infrastructure, design)
1. **意图检测** — 分类任务类型（新功能、bug 修复、重构、研究、测试、审查、文档、基础设施、设计）

2. **Scope Assessment** — Evaluate complexity (TRIVIAL / LOW / MEDIUM / HIGH / EPIC), using codebase size as signal if detected
2. **范围评估** — 评估复杂度（TRIVIAL / LOW / MEDIUM / HIGH / EPIC），如检测到则使用代码库大小作为信号

3. **ECC Component Matching** — Map to specific skills, commands, agents, and model tier
3. **ECC 组件匹配** — 映射到特定技能、命令、agent 和模型层级

4. **Missing Context Detection** — Identify gaps. If 3+ critical items missing, ask the user to clarify before generating
4. **缺失上下文检测** — 识别差距。如果缺少 3+ 关键项，在生成前询问用户澄清

5. **Workflow & Model** — Determine lifecycle position, recommend model tier, and split into multiple prompts if HIGH/EPIC
5. **工作流和模型** — 确定生命周期位置，推荐模型层级，如果是 HIGH/EPIC 则拆分为多个提示

## Output Requirements
## 输出要求

- Present diagnosis, recommended ECC components, and an optimized prompt using the Output Format from the prompt-optimizer skill
- 使用 prompt-optimizer 技能中的输出格式呈现诊断、推荐的 ECC 组件和优化后的提示

- Provide both **Full Version** (detailed) and **Quick Version** (compact, varied by intent type)
- 提供**完整版本**（详细）和**快速版本**（紧凑，按意图类型变化）

- Respond in the same language as the user's input
- 用用户输入的相同语言回复

- The optimized prompt must be complete and ready to copy-paste into a new session
- 优化后的提示必须完整且准备好粘贴到新会话中

- End with a footer offering adjustment or a clear next step for starting a separate execution request
- 以页脚结束，提供调整或明确的下一步以开始单独的执行请求

## CRITICAL
## 关键

Do NOT execute the user's task. Output ONLY the analysis and optimized prompt.
不要执行用户的任务。仅输出分析和优化后的提示。

If the user asks for direct execution, explain that `/prompt-optimize` only produces advisory output and tell them to start a normal task request instead.
如果用户要求直接执行，解释 `/prompt-optimize` 只产生咨询输出，并告诉他们改为启动正常的任务请求。

Note: `blueprint` is a **skill**, not a slash command. Write "Use the blueprint skill"
instead of presenting it as a `/...` command.
注意：`blueprint` 是一个**技能**，不是斜杠命令。写"使用 blueprint 技能"
而不是将其呈现为 `/...` 命令。

## User Input
## 用户输入

$ARGUMENTS
