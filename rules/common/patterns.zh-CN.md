---
name: Patterns
description: Common design and code patterns
description: 通用设计模式和代码模式
---

# Common Patterns
# 通用模式

## Skeleton Projects
## 骨架项目

When implementing new functionality:
实现新功能时：

1. Search for battle-tested skeleton projects
   搜索久经考验的骨架项目
2. Use parallel agents to evaluate options:
   使用并行 agent 评估选项：
   - Security assessment
     安全评估
   - Extensibility analysis
     可扩展性分析
   - Relevance scoring
     相关性评分
   - Implementation planning
     实现规划
3. Clone best match as foundation
   克隆最佳匹配作为基础
4. Iterate within proven structure
   在经过验证的结构中迭代

## Design Patterns
## 设计模式

### Repository Pattern
### 仓储模式

Encapsulate data access behind a consistent interface:
将数据访问封装在一致的接口后面：

- Define standard operations: findAll, findById, create, update, delete
  定义标准操作：findAll、findById、create、update、delete
- Concrete implementations handle storage details (database, API, file, etc.)
  具体实现处理存储细节（数据库、API、文件等）
- Business logic depends on the abstract interface, not the storage mechanism
  业务逻辑依赖抽象接口，而非存储机制
- Enables easy swapping of data sources and simplifies testing with mocks
  便于轻松切换数据源，并简化使用 mock 进行测试

### API Response Format
### API 响应格式

Use a consistent envelope for all API responses:
为所有 API 响应使用一致的包装：

- Include a success/status indicator
  包含成功/状态指示器
- Include the data payload (nullable on error)
  包含数据负载（错误时可为 null）
- Include an error message field (nullable on success)
  包含错误消息字段（成功时可为 null）
- Include metadata for paginated responses (total, page, limit)
  包含分页响应的元数据（total、page、limit）
