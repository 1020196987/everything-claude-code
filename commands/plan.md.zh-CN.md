---
description: Restate requirements, assess risks, and create step-by-step implementation plan. WAIT for user CONFIRM before touching any code.
description-zh: 重述需求，评估风险，并创建逐步实施计划。在接触任何代码之前等待用户确认。
---

# Plan Command
# Plan 命令

This command invokes the **planner** agent to create a comprehensive implementation plan before writing any code.
此命令调用 **planner** agent 在编写任何代码之前创建全面的实施计划。

## What This Command Does
## 此命令做什么

1. **Restate Requirements** - Clarify what needs to be built
   **重述需求** - 明确需要构建什么
2. **Identify Risks** - Surface potential issues and blockers
   **识别风险** - 发现潜在问题和阻碍
3. **Create Step Plan** - Break down implementation into phases
   **创建步骤计划** - 将实现分解为多个阶段
4. **Wait for Confirmation** - MUST receive user approval before proceeding
   **等待确认** - 必须收到用户批准后才能继续

## When to Use
## 使用场景

Use `/plan` when:
- Starting a new feature
- Making significant architectural changes
- Working on complex refactoring
- Multiple files/components will be affected
- Requirements are unclear or ambiguous

使用 `/plan` 当：
- 开始一个新功能
- 进行重大的架构变更
- 进行复杂的重构
- 会影响多个文件/组件
- 需求不明确或模糊

## How It Works
## 工作原理

The planner agent will:
1. **Analyze the request** and restate requirements in clear terms
   **分析请求**，用清晰的术语重述需求
2. **Break down into phases** with specific, actionable steps
   **分解为阶段**，每个阶段都有具体可操作的步骤
3. **Identify dependencies** between components
   **识别组件之间的依赖关系**
4. **Assess risks** and potential blockers
   **评估风险**和潜在阻碍
5. **Estimate complexity** (High/Medium/Low)
   **估算复杂度**（高/中/低）
6. **Present the plan** and WAIT for your explicit confirmation
   **展示计划**，并**等待**你明确确认

## Important Notes
## 重要注意事项

**CRITICAL**: The planner agent will **NOT** write any code until you explicitly confirm the plan with "yes" or "proceed" or similar affirmative response.
**关键**：**planner agent 不会**编写任何代码，直到你明确用 "yes" 或 "proceed" 或类似肯定响应确认计划。

If you want changes, respond with:
如果你想修改，回复：
- "modify: [your changes]"
- "different approach: [alternative]"
- "skip phase 2 and do phase 3 first"

## Integration with Other Commands
## 与其他命令的集成

After planning:
规划之后：
- Use `/tdd` to implement with test-driven development
  用 `/tdd` 通过测试驱动开发来实现
- Use `/build-fix` if build errors occur
  遇到构建错误时用 `/build-fix`
- Use `/code-review` to review completed implementation
  用 `/code-review` 审查完成的实现
