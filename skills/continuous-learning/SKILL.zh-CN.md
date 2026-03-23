---
name: continuous-learning
description: Automatically extract reusable patterns from Claude Code sessions and save them as learned skills for future use.
description zh-CN: 从 Claude Code 会话中自动提取可重用的模式，并将其保存为已学技能以供将来使用。
origin: ECC
---

# Continuous Learning Skill
# Continuous Learning Skill
# 持续学习技能

Automatically evaluates Claude Code sessions on end to extract reusable patterns that can be saved as learned skills.
## Automatically evaluates Claude Code sessions on end to extract reusable patterns that can be saved as learned skills.
## 在会话结束时自动评估 Claude Code 会话，提取可重用的模式并保存为已学技能。

## When to Activate
## When to Activate
## 何时激活

- Setting up automatic pattern extraction from Claude Code sessions
  - 从 Claude Code 会话中设置自动模式提取
- Configuring the Stop hook for session evaluation
  - 配置会话评估的 Stop hook
- Reviewing or curating learned skills in `~/.claude/skills/learned/`
  - 在 `~/.claude/skills/learned/` 中查看或整理已学技能
- Adjusting extraction thresholds or pattern categories
  - 调整提取阈值或模式类别
- Comparing v1 (this) vs v2 (instinct-based) approaches
  - 比较 v1（本技能）与 v2（基于本能）的方法

## How It Works
## How It Works
## 工作原理

This skill runs as a **Stop hook** at the end of each session:
## This skill runs as a **Stop hook** at the end of each session.
## 此技能在每个会话结束时作为 **Stop hook** 运行：

1. **Session Evaluation**: Checks if session has enough messages (default: 10+)
   - **会话评估**：检查会话是否有足够的消息（默认：10+）
2. **Pattern Detection**: Identifies extractable patterns from the session
   - **模式检测**：从会话中识别可提取的模式
3. **Skill Extraction**: Saves useful patterns to `~/.claude/skills/learned/`
   - **技能提取**：将有用的模式保存到 `~/.claude/skills/learned/`

## Configuration
## Configuration
## 配置

Edit `config.json` to customize:
## Edit `config.json` to customize:
## 编辑 `config.json` 进行自定义：

```json
{
  "min_session_length": 10,
  "extraction_threshold": "medium",
  "auto_approve": false,
  "learned_skills_path": "~/.claude/skills/learned/",
  "patterns_to_detect": [
    "error_resolution",
    "user_corrections",
    "workarounds",
    "debugging_techniques",
    "project_specific"
  ],
  "ignore_patterns": [
    "simple_typos",
    "one_time_fixes",
    "external_api_issues"
  ]
}
```

## Pattern Types
## Pattern Types
## 模式类型

| Pattern | Description |
|---------|-------------|
| `error_resolution` | How specific errors were resolved |
| `error_resolution` | 如何解决特定错误 |
| `user_corrections` | Patterns from user corrections |
| `user_corrections` | 用户纠正中的模式 |
| `workarounds` | Solutions to framework/library quirks |
| `workarounds` | 框架/库的变通方案 |
| `debugging_techniques` | Effective debugging approaches |
| `debugging_techniques` | 有效的调试方法 |
| `project_specific` | Project-specific conventions |
| `project_specific` | 项目特定的约定 |

## Hook Setup
## Hook Setup
## Hook 设置

Add to your `~/.claude/settings.json`:
## Add to your `~/.claude/settings.json`:
## 添加到 `~/.claude/settings.json`：

```json
{
  "hooks": {
    "Stop": [{
      "matcher": "*",
      "hooks": [{
        "type": "command",
        "command": "~/.claude/skills/continuous-learning/evaluate-session.sh"
      }]
    }]
  }
}
```

## Why Stop Hook?
## Why Stop Hook?
## 为什么使用 Stop Hook？

- **Lightweight**: Runs once at session end
  - **轻量级**：在会话结束时运行一次
- **Non-blocking**: Doesn't add latency to every message
  - **非阻塞**：不会为每条消息增加延迟
- **Complete context**: Has access to full session transcript
  - **完整上下文**：可访问完整会话记录

## Related
## Related
## 相关内容

- [The Longform Guide](https://x.com/affaanmustafa/status/2014040193557471352) - Section on continuous learning
  - [The Longform Guide](https://x.com/affaanmustafa/status/2014040193557471352) - 持续学习部分
- `/learn` command - Manual pattern extraction mid-session
  - `/learn` 命令 - 会话中手动模式提取

---

## Comparison Notes (Research: Jan 2025)
## Comparison Notes (Research: Jan 2025)
## 比较说明（研究时间：2025年1月）

### vs Homunculus
### vs Homunculus
### 与 Homunculus 对比

Homunculus v2 takes a more sophisticated approach:
## Homunculus v2 takes a more sophisticated approach.
## Homunculus v2 采用了更复杂的方法：

| Feature | Our Approach | Homunculus v2 |
|---------|--------------|---------------|
| Observation | Stop hook (end of session) | PreToolUse/PostToolUse hooks (100% reliable) |
| 观察方式 | Stop hook（会话结束时） | PreToolUse/PostToolUse hooks（100% 可靠） |
| Analysis | Main context | Background agent (Haiku) |
| 分析方式 | 主上下文 | 后台 agent（Haiku） |
| Granularity | Full skills | Atomic "instincts" |
| 粒度 | 完整技能 | 原子化"本能" |
| Confidence | None | 0.3-0.9 weighted |
| 置信度 | 无 | 0.3-0.9 加权 |
| Evolution | Direct to skill | Instincts -> cluster -> skill/command/agent |
| 进化方式 | 直接转为技能 | 本能 -> 聚类 -> 技能/命令/agent |
| Sharing | None | Export/import instincts |
| 共享方式 | 无 | 导出/导入本能 |

**Key insight from homunculus:**
## **Key insight from homunculus:**
## **来自 homunculus 的关键洞察：**

> "v1 relied on skills to observe. Skills are probabilistic—they fire ~50-80% of the time. v2 uses hooks for observation (100% reliable) and instincts as the atomic unit of learned behavior."
> "v1 依赖技能来观察。技能是概率性的——大约 50-80% 的时间会触发。v2 使用 hooks 进行观察（100% 可靠），并以本能作为学习行为的原子单位。"

### Potential v2 Enhancements
### Potential v2 Enhancements
### v2 潜在增强

1. **Instinct-based learning** - Smaller, atomic behaviors with confidence scoring
   - **基于本能的学习** - 更小的原子化行为，带有置信度评分
2. **Background observer** - Haiku agent analyzing in parallel
   - **后台观察器** - 并行分析的 Haiku agent
3. **Confidence decay** - Instincts lose confidence if contradicted
   - **置信度衰减** - 本能被反驳时会降低置信度
4. **Domain tagging** - code-style, testing, git, debugging, etc.
   - **领域标签** - code-style、testing、git、debugging 等
5. **Evolution path** - Cluster related instincts into skills/commands
   - **进化路径** - 将相关本能聚类为技能/命令

See: `docs/continuous-learning-v2-spec.md` for full spec.
## See: `docs/continuous-learning-v2-spec.md` for full spec.
## 详见：`docs/continuous-learning-v2-spec.md` 完整规格说明。
