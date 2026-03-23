---
name: Development Workflow
description: Full feature development workflow
description: 完整的功能开发工作流程
---

# Development Workflow
# 开发工作流程

> This file extends [common/git-workflow.md](./git-workflow.md) with the full feature development process that happens before git operations.
> 此文件扩展了 [common/git-workflow.md](./git-workflow.md)，包含 git 操作之前的完整功能开发流程。

The Feature Implementation Workflow describes the development pipeline: research, planning, TDD, code review, and then committing to git.
功能实现工作流程描述了开发管道：研究、规划、TDD、代码审查，然后提交到 git。

## Feature Implementation Workflow
## 功能实现工作流程

0. **Research & Reuse** _(mandatory before any new implementation)_
   **研究并复用** _(新实现前的强制步骤)_
   - **GitHub code search first:** Run `gh search repos` and `gh search code` to find existing implementations, templates, and patterns before writing anything new.
     **首先搜索 GitHub 代码：** 运行 `gh search repos` 和 `gh search code` 在编写新代码之前找到现有的实现、模板和模式。
   - **Library docs second:** Use Context7 or primary vendor docs to confirm API behavior, package usage, and version-specific details before implementing.
     **其次查看库文档：** 使用 Context7 或主要供应商文档确认 API 行为、包使用情况和版本特定细节，然后再实现。
   - **Exa only when the first two are insufficient:** Use Exa for broader web research or discovery after GitHub search and primary docs.
     **当前两者不足时使用 Exa：** 在 GitHub 搜索和主要文档之后，使用 Exa 进行更广泛的网页研究或发现。
   - **Check package registries:** Search npm, PyPI, crates.io, and other registries before writing utility code. Prefer battle-tested libraries over hand-rolled solutions.
     **检查包注册表：** 在编写工具代码之前搜索 npm、PyPI、crates.io 等注册表。优先选择久经考验的库而非自己手写的解决方案。
   - **Search for adaptable implementations:** Look for open-source projects that solve 80%+ of the problem and can be forked, ported, or wrapped.
     **搜索可适配的实现：** 寻找能解决 80% 以上问题的开源项目，可以 fork、移植或包装。
   - Prefer adopting or porting a proven approach over writing net-new code when it meets the requirement.
     当满足需求时，优先采用或移植经过验证的方法，而非编写全新的代码。

1. **Plan First**
   **首先规划**
   - Use **planner** agent to create implementation plan
     使用 **planner** agent 创建实现计划
   - Generate planning docs before coding: PRD, architecture, system_design, tech_doc, task_list
     在编码前生成规划文档：PRD、架构、system_design、tech_doc、task_list
   - Identify dependencies and risks
     识别依赖项和风险
   - Break down into phases
     分解为多个阶段

2. **TDD Approach**
   **TDD 方法**
   - Use **tdd-guide** agent
     使用 **tdd-guide** agent
   - Write tests first (RED)
     先写测试（RED）
   - Implement to pass tests (GREEN)
     实现以通过测试（GREEN）
   - Refactor (IMPROVE)
     重构（IMPROVE）
   - Verify 80%+ coverage
     验证 80%+ 覆盖率

3. **Code Review**
   **代码审查**
   - Use **code-reviewer** agent immediately after writing code
     编写代码后立即使用 **code-reviewer** agent
   - Address CRITICAL and HIGH issues
     解决 CRITICAL 和 HIGH 问题
   - Fix MEDIUM issues when possible
     尽可能修复 MEDIUM 问题

4. **Commit & Push**
   **提交并推送**
   - Detailed commit messages
     详细的提交消息
   - Follow conventional commits format
     遵循 conventional commits 格式
   - See [git-workflow.md](./git-workflow.md) for commit message format and PR process
     提交消息格式和 PR 流程参见 [git-workflow.md](./git-workflow.md)
