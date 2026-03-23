---
name: skill-create
description: Analyze local git history to extract coding patterns and generate SKILL.md files. Local version of the Skill Creator GitHub App.
allowed_tools: ["Bash", "Read", "Write", "Grep", "Glob"]
description-zh: 分析本地 git 历史以提取编码模式并生成 SKILL.md 文件。Skill Creator GitHub App 的本地版本。
---

# /skill-create - Local Skill Generation
# /skill-create - 本地技能生成

Analyze your repository's git history to extract coding patterns and generate SKILL.md files that teach Claude your team's practices.
分析你仓库的 git 历史以提取编码模式，并生成 SKILL.md 文件来教给 Claude 你团队的实践。

## Usage
## 使用方式

```bash
/skill-create                    # Analyze current repo
/skill-create --commits 100      # Analyze last 100 commits
/skill-create --output ./skills  # Custom output directory
/skill-create --instincts        # Also generate instincts for continuous-learning-v2
```

## What It Does
## 它做什么

1. **Parses Git History** - Analyzes commits, file changes, and patterns
   **解析 Git 历史** - 分析提交、文件更改和模式
2. **Detects Patterns** - Identifies recurring workflows and conventions
   **检测模式** - 识别重复的工作流和约定
3. **Generates SKILL.md** - Creates valid Claude Code skill files
   **生成 SKILL.md** - 创建有效的 Claude Code 技能文件
4. **Optionally Creates Instincts** - For the continuous-learning-v2 system
   **可选创建本能** - 用于 continuous-learning-v2 系统

## Analysis Steps
## 分析步骤

### Step 1: Gather Git Data
### 步骤 1：收集 Git 数据

```bash
# Get recent commits with file changes
git log --oneline -n ${COMMITS:-200} --name-only --pretty=format:"%H|%s|%ad" --date=short
```

### Step 2: Detect Patterns
### 步骤 2：检测模式

| Pattern | Detection Method |
|---------|-----------------|
| **Commit conventions** | Regex on commit messages (feat:, fix:, chore:) |
| **File co-changes** | Files that always change together |
| **Workflow sequences** | Repeated file change patterns |
| **Architecture** | Folder structure and naming conventions |
| **Testing patterns** | Test file locations, naming, coverage |

### Step 3: Generate SKILL.md
### 步骤 3：生成 SKILL.md

### Step 4: Generate Instincts (if --instincts)
### 步骤 4：生成本能（如果指定 --instincts）

## Related Commands
## 相关命令

- `/instinct-import` - Import generated instincts
  `/instinct-import` - 导入生成的本能
- `/instinct-status` - View learned instincts
  `/instinct-status` - 查看已学习的本能
- `/evolve` - Cluster instincts into skills/agents
  `/evolve` - 将本能聚合成技能/代理
