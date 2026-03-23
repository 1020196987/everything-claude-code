---
description: Answer a quick side question without interrupting or losing context from the current task. Resume work automatically after answering.
description-zh: 在不中断或丢失当前任务上下文的情况下快速回答附带问题。回答后自动恢复工作。
---

# Aside Command
# Aside 命令

Ask a question mid-task and get an immediate, focused answer — then continue right where you left off. The current task, files, and context are never modified.
在任务中途提出问题并获得即时、聚焦的答案——然后从中断的地方继续。当前任务、文件和上下文永远不会被修改。

## When to Use
## 使用场景

- You're curious about something while Claude is working and don't want to lose momentum
- 你在 Claude 工作时对某些东西感到好奇，不想失去动力

- You need a quick explanation of code Claude is currently editing
- 你需要快速解释 Claude 正在编辑的代码

- You want a second opinion or clarification on a decision without derailing the task
- 你需要对某个决定进行第二轮意见或澄清，而不会使任务脱轨

- You need to understand an error, concept, or pattern before Claude proceeds
- 你需要在 Claude 继续之前理解错误、概念或模式

- You want to ask something unrelated to the current task without starting a new session
- 你想提出与当前任务无关的问题，而不开启新会话

## Usage
## 使用方式

```
/aside <your question>
/aside what does this function actually return?
/aside is this pattern thread-safe?
/aside why are we using X instead of Y here?
/aside what's the difference between foo() and bar()?
/aside should we be worried about the N+1 query we just added?
```

## Process
## 流程

### Step 1: Freeze the current task state
### 步骤 1：冻结当前任务状态

Before answering anything, mentally note:
在回答任何问题之前，mental note：

- What is the active task? (what file, feature, or problem was being worked on)
- 什么是活动任务？（正在处理什么文件、功能或问题）

- What step was in progress at the moment `/aside` was invoked?
- 调用 `/aside` 时正在进行的步骤是什么？

- What was about to happen next?
- 接下来会发生什么？

Do NOT touch, edit, create, or delete any files during the aside.
在 aside 期间不要触摸、编辑、创建或删除任何文件。

### Step 2: Answer the question directly
### 步骤 2：直接回答问题

Answer the question in the most concise form that is still complete and useful.
以仍然完整且有用的最简洁形式回答问题。

- Lead with the answer, not the reasoning
- 先给出答案，而不是推理

- Keep it short — if a full explanation is needed, offer to go deeper after the task
- 保持简短 — 如果需要完整解释，在任务完成后提供深入讲解

- If the question is about the current file or code being worked on, reference it precisely (file path and line number if relevant)
- 如果问题是关于当前正在处理的文件或代码，请精确引用（如果相关，注明文件路径和行号）

- If answering requires reading a file, read it — but read only, never write
- 如果回答需要读取文件，请读取它 — 但只读不写

Format the response as:
格式化响应为：

```
ASIDE: [restate the question briefly]

[Your answer here]

— Back to task: [one-line description of what was being done]
```

### Step 3: Resume the main task
### 步骤 3：恢复主任务

After delivering the answer, immediately continue the active task from the exact point it was paused. Do not ask for permission to resume unless the aside answer revealed a blocker or a reason to reconsider the current approach (see Edge Cases).
交付答案后，立即从暂停的确切点继续活动任务。除非 aside 答案揭示了障碍或需要重新考虑当前方法的原因（见边缘情况），否则不要请求恢复许可。

---

## Edge Cases
## 边缘情况

**No question provided (`/aside` with nothing after it):**
**未提供问题（`/aside` 后面没有任何内容）：**

Respond:
回复：

```
ASIDE: no question provided

What would you like to know? (ask your question and I'll answer without losing the current task context)

— Back to task: [one-line description of what was being done]
```

**Question reveals a potential problem with the current task:**
**问题揭示了当前任务的潜在问题：**

Flag it clearly before resuming:
在恢复之前明确标记：

```
ASIDE: [answer]

⚠️ Note: This answer suggests [issue] with the current approach. Want to address this before continuing, or proceed as planned?
```

Wait for the user's decision before resuming.
等待用户决定后再恢复。

**Question is actually a task redirect (not a side question):**
**问题实际上是任务重定向（不是附带问题）：**

If the question implies changing what is being built (e.g., `/aside actually, let's use Redis instead`), clarify:
如果问题意味着改变正在构建的内容（例如，`/aside actually, let's use Redis instead`），请澄清：

```
ASIDE: That sounds like a direction change, not just a side question.
Do you want to:
  (a) Answer this as information only and keep the current plan
  (b) Pause the current task and change approach
```

Wait for the user's answer — do not make assumptions.
等待用户的回答 — 不要假设。

**Question is about the currently open file or code:**
**问题关于当前打开的文件或代码：**

Answer from the live context. If the file was read earlier in the session, reference it directly. If not, read it now (read-only) and answer with a file:line reference.
从实时上下文回答。如果文件在会话期间较早读取过，直接引用。如果没有，现在读取它（只读）并用 file:line 引用回答。

**No active task (nothing in progress when `/aside` is invoked):**
**没有活动任务（调用 `/aside` 时没有进行中的任务）：**

Still use the standard wrapper so the response shape stays consistent:
仍然使用标准包装器以保持响应形状一致：

```
ASIDE: [restate the question briefly]

[Your answer here]

— Back to task: no active task to resume
```

**Question requires a long answer:**
**问题需要长答案：**

Give the essential answer concisely, then offer:
简洁地给出基本答案，然后提供：

```
That's the short version. Want a deeper explanation after we finish [current task]?
```

**Multiple `/aside` questions in a row:**
**连续多个 `/aside` 问题：**

Answer each one in sequence. After the last answer, resume the main task. Do not lose task state across a chain of asides.
依次回答每个问题。回答完最后一个问题后，恢复主任务。不要在连续的 asides 中丢失任务状态。

**Aside answer implies a code change is needed:**
**Aside 答案暗示需要代码更改：**

Note the change needed but do not make it during the aside:
记录需要的更改，但在 aside 期间不要进行更改：

```
ASIDE: [answer]

📝 Worth fixing: [what should be changed]. I'll flag this after the current task unless you want to address it now.
```

**Question is ambiguous or too vague:**
**问题模糊或太模糊：**

Ask one clarifying question — the shortest question that gets the information needed to answer. Do not ask multiple questions.
提出一个澄清问题 — 获取回答所需信息的最短问题。不要提多个问题。

---

## Example Output
## 示例输出

```
User: /aside what does fetchWithRetry() actually do?

ASIDE: what does fetchWithRetry() do?

fetchWithRetry() (src/api/retry.ts:12) attempts the request up to 3 times with
exponential backoff (250ms → 500ms → 1s). It only retries on 5xx responses and
network errors — 4xx errors are treated as final and not retried.

— Back to task: refactoring the auth middleware in src/middleware/auth.ts
```

```
User: /aside is the approach we're taking thread-safe?

ASIDE: is the current approach thread-safe?

No — the shared cache object in src/cache/store.ts:34 is mutated without locking.
Under concurrent requests this is a race condition. It's low risk in a single-process
Node.js server but would be a real problem with worker threads or clustering.

⚠️ Note: This could affect the feature we're building. Want to address this now or continue and fix it in a follow-up?
```

---

## Notes
## 注意事项

- Never modify files during an aside — read-only access only
- 在 aside 期间永远不要修改文件 — 只读访问

- The aside is a conversation pause, not a new task — the original task must always resume
- aside 是对话暂停，不是新任务 — 原始任务必须始终恢复

- Keep answers focused: the goal is to unblock the user quickly, not to deliver a lecture
- 保持答案聚焦：目标是快速解锁用户，而不是进行讲座

- If an aside sparks a larger discussion, finish the current task first unless the aside reveals a blocker
- 如果 aside 引发更大讨论，先完成当前任务，除非 aside 揭示了障碍

- Asides are not saved to session files unless explicitly relevant to the task outcome
- 除非与任务结果明确相关，否则 aside 不会保存到会话文件
