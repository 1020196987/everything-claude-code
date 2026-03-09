# Rules 详解

## 什么是 Rules？

**Rules** 是 Claude Code 的规则集，定义了代码开发的标准、约定和检查清单。它与 Skills 不同：

| | Rules | Skills |
|---|---|---|
| 定义 | **标准、约定**（what to do） | **具体参考材料**（how to do it） |
| 触发 | 自动加载 | 需要用 /skill 调用 |
| 示例 | "使用不可变性"、"80% 覆盖率" | `python-patterns`、`golang-testing` |

## Rules 目录结构

```
rules/
├── common/          # 通用规则（所有语言适用）
│   ├── coding-style.md
│   ├── git-workflow.md
│   ├── testing.md
│   ├── security.md
│   ├── performance.md
│   ├── patterns.md
│   ├── development-workflow.md
│   ├── hooks.md
│   └── agents.md
├── typescript/      # TypeScript 专用规则
├── python/          # Python 专用规则
├── golang/          # Go 专用规则
└── swift/           # Swift 专用规则
```

## common 规则详解

### 1. coding-style.md - 代码风格

**核心原则：不可变性（CRITICAL）**
- 始终创建新对象，**永不修改现有对象**
- 使用展开运算符（spread operator）进行不可变更新
- 原因：防止隐藏副作用，使调试更容易，支持安全并发

```typescript
// 错误：修改原对象
function updateUser(user, name) {
  user.name = name  // 突变！
  return user
}

// 正确：返回新对象
function updateUser(user, name) {
  return {
    ...user,
    name
  }
}
```

**文件组织**
- 多小文件 > 少大文件
- 典型 200-400 行，最多 800 行
- 按功能/领域组织，而非按类型

**错误处理**
- 每个层级都要显式处理错误
- UI 代码提供用户友好的错误信息
- 服务器端记录详细错误上下文
- 永不静默吞掉错误

**输入验证**
- 在系统边界验证所有输入
- 使用 schema-based 验证（如 Zod）
- 快速失败并给出清晰错误信息

### 2. testing.md - 测试要求

**最低测试覆盖率：80%**

三种测试类型（全部需要）：
1. **单元测试** - 单个函数、工具、组件
2. **集成测试** - API 端点、数据库操作
3. **E2E 测试** - 关键用户流程

**TDD 工作流（强制）**
1. 先写测试（RED）
2. 运行测试 - 应该失败
3. 写最小实现（GREEN）
4. 运行测试 - 应该通过
5. 重构（IMPROVE）
6. 验证覆盖率（80%+）

### 3. security.md - 安全规范

**提交前必检**
- [ ] 无硬编码密钥（API keys、passwords、tokens）
- [ ] 所有用户输入已验证
- [ ] SQL 注入预防（参数化查询）
- [ ] XSS 预防（HTML 净化）
- [ ] CSRF 保护已启用
- [ ] 身份验证/授权已验证
- [ ] 所有端点有限流
- [ ] 错误信息不泄露敏感数据

**密钥管理**
- 永不硬编码密钥
- 始终使用环境变量或密钥管理器
- 启动时验证必需密钥存在

### 4. git-workflow.md - Git 工作流

**提交信息格式**
```
<type>: <description>

<optional body>
```

类型：feat, fix, refactor, docs, test, chore, perf, ci

**PR 工作流**
1. 分析完整提交历史（不仅是最新提交）
2. 使用 `git diff [base-branch]...HEAD` 查看所有更改
3. 起草全面的 PR 总结
4. 包含测试计划
5. 新分支使用 `-u` 标志推送

### 5. development-workflow.md - 开发工作流

**完整开发流程：**

0. **研究 & 复用**（实现前的强制步骤）
   - GitHub 代码搜索
   - 使用 Exa MCP 进行研究
   - 检查包注册表（npm、PyPI 等）
   - 优先使用经过验证的方案

1. **先规划**
   - 使用 planner agent 创建实施计划
   - 生成规划文档：PRD、架构、技术设计
   - 识别依赖和风险

2. **TDD 方式**
   - 使用 tdd-guide agent
   - 先写测试
   - 实现通过测试
   - 重构
   - 验证 80%+ 覆盖率

3. **代码审查**
   - 写完代码后立即使用 code-reviewer agent
   - 解决 CRITICAL 和 HIGH 问题
   - 尽可能修复 MEDIUM 问题

4. **提交 & 推送**
   - 详细提交信息
   - 遵循约定式提交格式

### 6. performance.md - 性能优化

**模型选择策略**
- **Haiku 4.5**：轻量级 agents、频繁调用、配对编程
- **Sonnet 4.6**：主要开发工作、多 agent 编排、复杂编码任务
- **Opus 4.5**：复杂架构决策、最大推理需求、研究分析任务

**上下文窗口管理**
- 最后 20% 上下文避免：
  - 大规模重构
  - 跨多文件的功能实现
  - 调试复杂交互

**扩展思考**
- 默认启用，保留最多 31,999 tokens 用于内部推理
- 通过 Option+T (macOS) / Alt+T (Windows/Linux) 切换
- 复杂任务启用 Plan Mode

### 7. patterns.md - 通用模式

**骨架项目**
1. 搜索经过验证的骨架项目
2. 使用并行 agents 评估选项
3. 克隆最佳匹配作为基础
4. 在已验证的结构中迭代

**设计模式**
- **Repository 模式**：封装数据访问在一致的接口后
- **API 响应格式**：统一所有 API 响应格式

## TypeScript 规则详解

### typescript/coding-style.md

继承自 common/coding-style.md，添加 TypeScript 特定内容：

**不可变性**
```typescript
// 使用展开运算符进行不可变更新
const updated = { ...original, field: newValue }
```

**错误处理**
```typescript
try {
  const result = await riskyOperation()
  return result
} catch (error) {
  console.error('Operation failed:', error)
  throw new Error('用户友好的错误信息')
}
```

**输入验证**
- 使用 **Zod** 进行 schema-based 验证

**Console.log**
- 生产代码中不使用 console.log
- 使用专业的日志库

### typescript/testing.md

继承自 common/testing.md：

**E2E 测试**
- 使用 **Playwright** 作为 E2E 测试框架
- 用于关键用户流程

**Agent 支持**
- **e2e-runner**：Playwright E2E 测试专家

## 安装和使用

### 安装
```bash
./install.sh typescript python
```

### 触发时机
- **自动加载**：每次在项目目录运行 Claude Code 时自动应用
- **无需手动调用**：像内置约束一样在后台运行
- **项目级规则**：项目根目录的 `.claude/rules/` 优先级最高

### 加载顺序
```
~/.claude/rules/common/        # 始终加载
~/.claude/rules/typescript/    # 根据项目语言加载
项目根目录/.claude/rules/      # 项目特定规则（优先级最高）
```

## 总结

Rules 是 Claude Code 的"开发宪法"：
- 定义**必须遵守**的标准和约定
- 自动应用到所有代码生成和审查
- 支持语言特定扩展（common + typescript/python/golang/swift）
- 与 Skills（具体操作指南）配合使用
