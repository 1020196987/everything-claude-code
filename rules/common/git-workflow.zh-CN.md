---
name: Git Workflow
description: Git commit and PR workflow
description: Git 提交和 PR 工作流
---

# Git Workflow
# Git 工作流

## Commit Message Format
## 提交消息格式

```
<type>: <description>

<optional body>
```

```
<类型>: <描述>

<可选正文>
```

Types: feat, fix, refactor, docs, test, chore, perf, ci
类型：feat, fix, refactor, docs, test, chore, perf, ci

Note: Attribution disabled globally via ~/.claude/settings.json.
注意：通过 ~/.claude/settings.json 全局禁用归属。

## Pull Request Workflow
## 拉取请求工作流

When creating PRs:
创建 PR 时：

1. Analyze full commit history (not just latest commit)
   分析完整的提交历史（不仅仅是最新提交）
2. Use `git diff [base-branch]...HEAD` to see all changes
   使用 `git diff [base-branch]...HEAD` 查看所有更改
3. Draft comprehensive PR summary
   起草全面的 PR 总结
4. Include test plan with TODOs
   包含带有 TODO 的测试计划
5. Push with `-u` flag if new branch
   如果是新分支，使用 `-u` 标志推送

> For the full development process (planning, TDD, code review) before git operations,
> see [development-workflow.md](./development-workflow.md).
> 有关 git 操作之前的完整开发流程（规划、TDD、代码审查），
> 参见 [development-workflow.md](./development-workflow.md)。
