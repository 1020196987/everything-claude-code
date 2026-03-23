---
name: Coding Style
description: Common coding style guidelines
description: 通用编码风格规范
---

# Coding Style
# 编码风格

## Immutability (CRITICAL)
## 不可变性（关键）

ALWAYS create new objects, NEVER mutate existing ones:
始终创建新对象，永不修改现有对象：

```
// Pseudocode
WRONG:  modify(original, field, value) → changes original in-place
CORRECT: update(original, field, value) → returns new copy with change
```

Rationale: Immutable data prevents hidden side effects, makes debugging easier, and enables safe concurrency.
理由：不可变数据防止隐藏的副作用，使调试更容易，并支持安全的并发。

## File Organization
## 文件组织

MANY SMALL FILES > FEW LARGE FILES:
多个小文件 > 少数大文件：

- High cohesion, low coupling
  高内聚，低耦合
- 200-400 lines typical, 800 max
  通常 200-400 行，最多 800 行
- Extract utilities from large modules
  从大模块中提取工具函数
- Organize by feature/domain, not by type
  按功能/领域组织，而非按类型

## Error Handling
## 错误处理

ALWAYS handle errors comprehensively:
始终全面处理错误：

- Handle errors explicitly at every level
  在每一层明确处理错误
- Provide user-friendly error messages in UI-facing code
  在面向用户的代码中提供友好的错误消息
- Log detailed error context on the server side
  在服务器端记录详细的错误上下文
- Never silently swallow errors
  永不吞掉错误

## Input Validation
## 输入验证

ALWAYS validate at system boundaries:
始终在系统边界验证：

- Validate all user input before processing
  在处理前验证所有用户输入
- Use schema-based validation where available
  在可用时使用基于模式的验证
- Fail fast with clear error messages
  快速失败并提供清晰的错误消息
- Never trust external data (API responses, user input, file content)
  永不信任外部数据（API 响应、用户输入、文件内容）

## Code Quality Checklist
## 代码质量检查清单

Before marking work complete:
在标记工作完成前：

- [ ] Code is readable and well-named
  [ ] 代码可读且命名良好
- [ ] Functions are small (<50 lines)
  [ ] 函数短小（<50 行）
- [ ] Files are focused (<800 lines)
  [ ] 文件专注（<800 行）
- [ ] No deep nesting (>4 levels)
  [ ] 无深层嵌套（>4 层）
- [ ] Proper error handling
  [ ] 正确的错误处理
- [ ] No hardcoded values (use constants or config)
  [ ] 无硬编码值（使用常量或配置）
- [ ] No mutation (immutable patterns used)
  [ ] 无变更（使用不可变模式）
