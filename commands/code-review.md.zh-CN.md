---
description: Comprehensive security and quality review of uncommitted changes. Blocks commit if CRITICAL or HIGH issues found.
description-zh: 对未提交更改进行综合安全和质量审查。如果发现 CRITICAL 或 HIGH 问题则阻止提交。
---

# Code Review
# 代码审查

Comprehensive security and quality review of uncommitted changes:
对未提交更改的综合安全和质量审查：

1. Get changed files: `git diff --name-only HEAD`
   获取更改的文件：`git diff --name-only HEAD`

2. For each changed file, check for:
   对于每个更改的文件，检查：

**Security Issues (CRITICAL):**
**安全问题（严重）：**

- Hardcoded credentials, API keys, tokens
  硬编码凭证、API 密钥、令牌
- SQL injection vulnerabilities
  SQL 注入漏洞
- XSS vulnerabilities
  XSS 漏洞
- Missing input validation
  缺少输入验证
- Insecure dependencies
  不安全的依赖
- Path traversal risks
  路径遍历风险

**Code Quality (HIGH):**
**代码质量（高）：**

- Functions > 50 lines
  函数 > 50 行
- Files > 800 lines
  文件 > 800 行
- Nesting depth > 4 levels
  嵌套深度 > 4 层
- Missing error handling
  缺少错误处理
- console.log statements
  console.log 语句
- TODO/FIXME comments
  TODO/FIXME 注释
- Missing JSDoc for public APIs
  公共 API 缺少 JSDoc

**Best Practices (MEDIUM):**
**最佳实践（中等）：**

- Mutation patterns (use immutable instead)
  变更模式（改用不可变）
- Emoji usage in code/comments
  代码/注释中的表情符号使用
- Missing tests for new code
  新代码缺少测试
- Accessibility issues (a11y)
  无障碍问题 (a11y)

3. Generate report with:
   生成报告，包含：
   - Severity: CRITICAL, HIGH, MEDIUM, LOW
     严重程度：CRITICAL、HIGH、MEDIUM、LOW
   - File location and line numbers
     文件位置和行号
   - Issue description
     问题描述
   - Suggested fix
     建议修复

4. Block commit if CRITICAL or HIGH issues found
   如果发现 CRITICAL 或 HIGH 问题则阻止提交

Never approve code with security vulnerabilities!
永远不要批准存在安全漏洞的代码！
