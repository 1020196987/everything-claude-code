---
name: skill-stocktake
description: "Use when auditing Claude skills and commands for quality. Supports Quick Scan (changed skills only) and Full Stocktake modes with sequential subagent batch evaluation."
description zh-CN: "在审计 Claude skills 和 commands 的质量时使用。支持快速扫描（仅更改的 skills）和完整盘点模式，带有顺序子 Agent 批量评估。"
origin: ECC
---

# skill-stocktake
# skill-stocktake 技能

Slash command (`/skill-stocktake`) that audits all Claude skills and commands using a quality checklist + AI holistic judgment. Supports two modes: Quick Scan for recently changed skills, and Full Stocktake for a complete review.
斜杠命令（`/skill-stocktake`）使用质量检查清单 + AI 整体判断审计所有 Claude skills 和 commands。支持两种模式：快速扫描（最近更改的 skills）和完整盘点（全面审查）。

## Scope
## 范围

The command targets the following paths **relative to the directory where it is invoked**:
该命令针对**相对于其调用目录**的以下路径：

| Path | Description |
|------|-------------|
| `~/.claude/skills/` | Global skills (all projects) |
| `{cwd}/.claude/skills/` | Project-level skills (if the directory exists) |

| 路径 | 描述 |
|------|-------------|
| `~/.claude/skills/` | 全局 skills（所有项目） |
| `{cwd}/.claude/skills/` | 项目级 skills（如果目录存在） |

**At the start of Phase 1, the command explicitly lists which paths were found and scanned.**
**在第 1 阶段开始时，命令明确列出找到并扫描的路径。**

### Targeting a specific project
### 定位特定项目

To include project-level skills, run from that project's root directory:
要包含项目级 skills，请从该项目的根目录运行：

```bash
cd ~/path/to/my-project
/skill-stocktake
```

```bash
cd ~/path/to/my-project
/skill-stocktake
```

If the project has no `.claude/skills/` directory, only global skills and commands are evaluated.
如果项目没有 `.claude/skills/` 目录，则仅评估全局 skills 和 commands。

## Modes
## 模式

| Mode | Trigger | Duration |
|------|---------|---------|
| Quick Scan | `results.json` exists (default) | 5–10 min |
| Full Stocktake | `results.json` absent, or `/skill-stocktake full` | 20–30 min |

| 模式 | 触发条件 | 持续时间 |
|------|---------|---------|
| 快速扫描 | `results.json` 存在（默认） | 5-10 分钟 |
| 完整盘点 | `results.json` 不存在，或 `/skill-stocktake full` | 20-30 分钟 |

**Results cache:** `~/.claude/skills/skill-stocktake/results.json`
**结果缓存：** `~/.claude/skills/skill-stocktake/results.json`

## Quick Scan Flow
## 快速扫描流程

Re-evaluate only skills that have changed since the last run (5–10 min).
仅重新评估自上次运行以来更改的 skills（5-10 分钟）。

1. Read `~/.claude/skills/skill-stocktake/results.json`
1. 读取 `~/.claude/skills/skill-stocktake/results.json`
2. Run: `bash ~/.claude/skills/skill-stocktake/scripts/quick-diff.sh \
         ~/.claude/skills/skill-stocktake/results.json`
2. 运行：`bash ~/.claude/skills/skill-stocktake/scripts/quick-diff.sh \
         ~/.claude/skills/skill-stocktake/results.json`
   (Project dir is auto-detected from `$PWD/.claude/skills`; pass it explicitly only if needed)
   （项目目录从 `$PWD/.claude/skills` 自动检测；仅在需要时显式传递）
3. If output is `[]`: report "No changes since last run." and stop
3. 如果输出为 `[]`：报告"自上次运行以来无更改。"并停止
4. Re-evaluate only those changed files using the same Phase 2 criteria
4. 使用相同的第 2 阶段标准仅重新评估这些更改的文件
5. Carry forward unchanged skills from previous results
5. 从之前的结果中保留未更改的 skills
6. Output only the diff
6. 仅输出差异
7. Run: `bash ~/.claude/skills/skill-stocktake/scripts/save-results.sh \
         ~/.claude/skills/skill-stocktake/results.json <<< "$EVAL_RESULTS"`
7. 运行：`bash ~/.claude/skills/skill-stocktake/scripts/save-results.sh \
         ~/.claude/skills/skill-stocktake/results.json <<< "$EVAL_RESULTS"`

## Full Stocktake Flow
## 完整盘点流程

### Phase 1 — Inventory
### 第 1 阶段 — 清单

Run: `bash ~/.claude/skills/skill-stocktake/scripts/scan.sh`
运行：`bash ~/.claude/skills/skill-stocktake/scripts/scan.sh`

The script enumerates skill files, extracts frontmatter, and collects UTC mtimes.
该脚本枚举 skill 文件，提取 frontmatter，并收集 UTC mtimes。
Project dir is auto-detected from `$PWD/.claude/skills`; pass it explicitly only if needed.
项目目录从 `$PWD/.claude/skills` 自动检测；仅在需要时显式传递。
Present the scan summary and inventory table from the script output:
从脚本输出中展示扫描摘要和清单表：

```
Scanning:
  ✓ ~/.claude/skills/         (17 files)
  ✗ {cwd}/.claude/skills/    (not found — global skills only)
```

```
扫描中：
  ✓ ~/.claude/skills/         (17 个文件)
  ✗ {cwd}/.claude/skills/    (未找到 — 仅全局 skills)
```

| Skill | 7d use | 30d use | Description |
|-------|--------|---------|-------------|

### Phase 2 — Quality Evaluation
### 第 2 阶段 — 质量评估

Launch an Agent tool subagent (**general-purpose agent**) with the full inventory and checklist:
使用完整清单和检查清单启动一个 Agent 工具子代理（**通用代理**）：

```text
Agent(
  subagent_type="general-purpose",
  prompt="
Evaluate the following skill inventory against the checklist.

[INVENTORY]

[CHECKLIST]

Return JSON for each skill:
{ \"verdict\": \"Keep\"|\"Improve\"|\"Update\"|\"Retire\"|\"Merge into [X]\", \"reason\": \"...\" }
"
)
```

The subagent reads each skill, applies the checklist, and returns per-skill JSON:
子代理读取每个 skill，应用检查清单，并返回每个 skill 的 JSON：

`{ "verdict": "Keep"|"Improve"|"Update"|"Retire"|"Merge into [X]", "reason": "..." }`

**Chunk guidance:** Process ~20 skills per subagent invocation to keep context manageable. Save intermediate results to `results.json` (`status: "in_progress"`) after each chunk.
**分块指导：** 每次子代理调用处理约 20 个 skills 以保持上下文可管理。每批次后将中间结果保存到 `results.json`（`status: "in_progress"`）。

After all skills are evaluated: set `status: "completed"`, proceed to Phase 3.
评估完所有 skills 后：设置 `status: "completed"`，进入第 3 阶段。

**Resume detection:** If `status: "in_progress"` is found on startup, resume from the first unevaluated skill.
**恢复检测：** 如果在启动时发现 `status: "in_progress"`，从第一个未评估的 skill 恢复。

Each skill is evaluated against this checklist:
每个 skill 都根据此检查清单进行评估：

```
- [ ] Content overlap with other skills checked
- [ ] Overlap with MEMORY.md / CLAUDE.md checked
- [ ] Freshness of technical references verified (use WebSearch if tool names / CLI flags / APIs are present)
- [ ] Usage frequency considered
```

```
- [ ] 检查与其他 skills 的内容重叠
- [ ] 检查与 MEMORY.md / CLAUDE.md 的重叠
- [ ] 验证技术引用的时效性（如果存在工具名称/CLI 标志/API，请使用 WebSearch）
- [ ] 考虑使用频率
```

Verdict criteria:
裁决标准：

| Verdict | Meaning |
|---------|---------|
| Keep | Useful and current |
| Improve | Worth keeping, but specific improvements needed |
| Update | Referenced technology is outdated (verify with WebSearch) |
| Retire | Low quality, stale, or cost-asymmetric |
| Merge into [X] | Substantial overlap with another skill; name the merge target |

| 裁决 | 含义 |
|---------|---------|
| Keep | 有用且最新 |
| Improve | 值得保留，但需要具体改进 |
| Update | 引用的技术已过时（使用 WebSearch 验证） |
| Retire | 质量低、过期或成本不对称 |
| Merge into [X] | 与另一个 skill 有实质性重叠；命名合并目标 |

Evaluation is **holistic AI judgment** — not a numeric rubric. Guiding dimensions:
评估是**整体 AI 判断** — 不是数字评分表。指导维度：

- **Actionability**: code examples, commands, or steps that let you act immediately
- **可操作性**：代码示例、命令或让您立即采取行动的步骤
- **Scope fit**: name, trigger, and content are aligned; not too broad or narrow
- **范围匹配**：名称、触发器和内容一致；不太宽泛也不太狭窄
- **Uniqueness**: value not replaceable by MEMORY.md / CLAUDE.md / another skill
- **独特性**：价值不能被 MEMORY.md / CLAUDE.md / 另一个 skill 替代
- **Currency**: technical references work in the current environment
- **时效性**：技术引用在当前环境中有效

**Reason quality requirements** — the `reason` field must be self-contained and decision-enabling:
**理由质量要求** — `reason` 字段必须自包含且能支持决策：

- Do NOT write "unchanged" alone — always restate the core evidence
- 不要单独写"未更改" — 始终重述核心证据
- For **Retire**: state (1) what specific defect was found, (2) what covers the same need instead
- 对于 **Retire**：说明（1）发现了什么具体缺陷，（2）有什么可以替代满足相同需求
  - Bad: `"Superseded"`
  - 不好：`"Superseded"`
  - Good: `"disable-model-invocation: true already set; superseded by continuous-learning-v2 which covers all the same patterns plus confidence scoring. No unique content remains."`
  - 好：`"disable-model-invocation: true already set; superseded by continuous-learning-v2 which covers all the same patterns plus confidence scoring. No unique content remains."`
- For **Merge**: name the target and describe what content to integrate
- 对于 **Merge**：命名目标并描述要整合的内容
  - Bad: `"Overlaps with X"`
  - 不好：`"Overlaps with X"`
  - Good: `"42-line thin content; Step 4 of chatlog-to-article already covers the same workflow. Integrate the 'article angle' tip as a note in that skill."`
  - 好：`"42-line thin content; Step 4 of chatlog-to-article already covers the same workflow. Integrate the 'article angle' tip as a note in that skill."`
- For **Improve**: describe the specific change needed (what section, what action, target size if relevant)
- 对于 **Improve**：描述需要的具体更改（哪个部分、什么行动、相关目标大小）
  - Bad: `"Too long"`
  - 不好：`"Too long"`
  - Good: `"276 lines; Section 'Framework Comparison' (L80–140) duplicates ai-era-architecture-principles; delete it to reach ~150 lines."`
  - 好：`"276 lines; Section 'Framework Comparison' (L80–140) duplicates ai-era-architecture-principles; delete it to reach ~150 lines."`
- For **Keep** (mtime-only change in Quick Scan): restate the original verdict rationale, do not write "unchanged"
- 对于 **Keep**（快速扫描中仅 mtime 更改）：重述原始裁决理由，不要写"未更改"
  - Bad: `"Unchanged"`
  - 不好：`"Unchanged"`
  - Good: `"mtime updated but content unchanged. Unique Python reference explicitly imported by rules/python/; no overlap found."`
  - 好：`"mtime updated but content unchanged. Unique Python reference explicitly imported by rules/python/; no overlap found."`

### Phase 3 — Summary Table
### 第 3 阶段 — 摘要表

| Skill | 7d use | Verdict | Reason |
|-------|--------|---------|--------|

| Skill | 7 天使用 | 裁决 | 理由 |
|-------|--------|---------|--------|

### Phase 4 — Consolidation
### 第 4 阶段 — 整合

1. **Retire / Merge**: present detailed justification per file before confirming with user:
   - What specific problem was found (overlap, staleness, broken references, etc.)
   - What alternative covers the same functionality (for Retire: which existing skill/rule; for Merge: the target file and what content to integrate)
   - Impact of removal (any dependent skills, MEMORY.md references, or workflows affected)
1. **Retire / Merge**：在获得用户确认之前，呈现每个文件的详细理由：
   - 发现了什么具体问题（重叠、过时、损坏的引用等）
   - 什么替代方案涵盖相同功能（对于 Retire：哪个现有 skill/rule；对于 Merge：目标文件以及要整合什么内容）
   - 删除的影响（任何依赖的 skills、 MEMORY.md 引用或受影响的工作流程）
2. **Improve**: present specific improvement suggestions with rationale:
   - What to change and why (e.g., "trim 430→200 lines because sections X/Y duplicate python-patterns")
   - User decides whether to act
2. **Improve**：呈现具体的改进建议及理由：
   - 要更改什么以及为什么（例如，"从 430 行缩减到 200 行，因为 X/Y 部分与 python-patterns 重复"）
   - 由用户决定是否采取行动
3. **Update**: present updated content with sources checked
3. **Update**：呈现已检查来源的更新内容
4. Check MEMORY.md line count; propose compression if >100 lines
4. 检查 MEMORY.md 行数；如果 >100 行则建议压缩

## Results File Schema
## 结果文件模式

`~/.claude/skills/skill-stocktake/results.json`:
`~/.claude/skills/skill-stocktake/results.json`：

**`evaluated_at`**: Must be set to the actual UTC time of evaluation completion.
**`evaluated_at`**：必须设置为评估完成的实际 UTC 时间。
Obtain via Bash: `date -u +%Y-%m-%dT%H:%M:%SZ`. Never use a date-only approximation like `T00:00:00Z`.
通过 Bash 获取：`date -u +%Y-%m-%dT%H:%M:%SZ`。永远不要使用仅日期的近似值如 `T00:00:00Z`。

```json
{
  "evaluated_at": "2026-02-21T10:00:00Z",
  "mode": "full",
  "batch_progress": {
    "total": 80,
    "evaluated": 80,
    "status": "completed"
  },
  "skills": {
    "skill-name": {
      "path": "~/.claude/skills/skill-name/SKILL.md",
      "verdict": "Keep",
      "reason": "Concrete, actionable, unique value for X workflow",
      "mtime": "2026-01-15T08:30:00Z"
    }
  }
}
```

```json
{
  "evaluated_at": "2026-02-21T10:00:00Z",
  "mode": "full",
  "batch_progress": {
    "total": 80,
    "evaluated": 80,
    "status": "completed"
  },
  "skills": {
    "skill-name": {
      "path": "~/.claude/skills/skill-name/SKILL.md",
      "verdict": "Keep",
      "reason": "Concrete, actionable, unique value for X workflow",
      "mtime": "2026-01-15T08:30:00Z"
    }
  }
}
```

## Notes
## 备注

- Evaluation is blind: the same checklist applies to all skills regardless of origin (ECC, self-authored, auto-extracted)
- 评估是盲目的：相同的检查清单适用于所有 skills，无论来源如何（ECC、自创、自动提取）
- Archive / delete operations always require explicit user confirmation
- 归档/删除操作始终需要用户的明确确认
- No verdict branching by skill origin
- 不按 skill 来源进行裁决分支
