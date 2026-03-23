---
name: postgres-patterns
description: PostgreSQL database patterns for query optimization, schema design, indexing, and security. Based on Supabase best practices.
description zh-CN: PostgreSQL 数据库模式，涵盖查询优化、模式设计、索引和安全。基于 Supabase 最佳实践。
origin: ECC
---

# PostgreSQL Patterns

## PostgreSQL Patterns
## PostgreSQL 模式

Quick reference for PostgreSQL best practices. For detailed guidance, use the `database-reviewer` agent.
PostgreSQL 最佳实践快速参考。详细指导请使用 `database-reviewer` agent。

## When to Activate

## When to Activate
## 何时激活

- Writing SQL queries or migrations
- 编写 SQL 查询或迁移
- Designing database schemas
- 设计数据库模式
- Troubleshooting slow queries
- 排查慢查询
- Implementing Row Level Security
- 实现行级安全
- Setting up connection pooling
- 设置连接池

## Quick Reference

## Quick Reference
## 快速参考

### Index Cheat Sheet

### Index Cheat Sheet
### 索引速查表

| Query Pattern | Index Type | Example |
| 查询模式 | 索引类型 | 示例 |
|--------------|------------|---------|
| `WHERE col = value` | B-tree (default) / B-tree（默认） | `CREATE INDEX idx ON t (col)` |
| `WHERE col > value` | B-tree | `CREATE INDEX idx ON t (col)` |
| `WHERE a = x AND b > y` | Composite / 复合索引 | `CREATE INDEX idx ON t (a, b)` |
| `WHERE jsonb @> '{}'` | GIN | `CREATE INDEX idx ON t USING gin (col)` |
| `WHERE tsv @@ query` | GIN | `CREATE INDEX idx ON t USING gin (col)` |
| Time-series ranges / 时间序列范围 | BRIN | `CREATE INDEX idx ON t USING brin (col)` |

### Data Type Quick Reference

### Data Type Quick Reference
### 数据类型速查表

| Use Case | Correct Type | Avoid |
| 使用场景 | 正确类型 | 避免 |
|----------|-------------|-------|
| IDs | `bigint` | `int`, random UUID |
| 字符串 | `text` | `varchar(255)` |
| 时间戳 | `timestamptz` | `timestamp` |
| 金额 | `numeric(10,2)` | `float` |
| 标志 | `boolean` | `varchar`, `int` |

### Common Patterns

### Common Patterns
### 常见模式

**Composite Index Order:**
**复合索引顺序：**
```sql
-- Equality columns first, then range columns
-- 等值列在前，范围列在后
CREATE INDEX idx ON orders (status, created_at);
-- Works for: WHERE status = 'pending' AND created_at > '2024-01-01'
```

**Covering Index:**
**覆盖索引：**
```sql
CREATE INDEX idx ON users (email) INCLUDE (name, created_at);
-- Avoids table lookup for SELECT email, name, created_at
-- 对 SELECT email, name, created_at 避免表查找
```

**Partial Index:**
**部分索引：**
```sql
CREATE INDEX idx ON users (email) WHERE deleted_at IS NULL;
-- Smaller index, only includes active users
-- 更小的索引，仅包含活跃用户
```

**RLS Policy (Optimized):**
**RLS 策略（优化版）：**
```sql
CREATE POLICY policy ON orders
  USING ((SELECT auth.uid()) = user_id);  -- Wrap in SELECT!
```

**UPSERT:**
```sql
INSERT INTO settings (user_id, key, value)
VALUES (123, 'theme', 'dark')
ON CONFLICT (user_id, key)
DO UPDATE SET value = EXCLUDED.value;
```

**Cursor Pagination:**
**游标分页：**
```sql
SELECT * FROM products WHERE id > $last_id ORDER BY id LIMIT 20;
-- O(1) vs OFFSET which is O(n)
```

**Queue Processing:**
**队列处理：**
```sql
UPDATE jobs SET status = 'processing'
WHERE id = (
  SELECT id FROM jobs WHERE status = 'pending'
  ORDER BY created_at LIMIT 1
  FOR UPDATE SKIP LOCKED
) RETURNING *;
```

### Anti-Pattern Detection

### Anti-Pattern Detection
### 反模式检测

```sql
-- Find unindexed foreign keys
-- 查找未索引的外键
SELECT conrelid::regclass, a.attname
FROM pg_constraint c
JOIN pg_attribute a ON a.attrelid = c.conrelid AND a.attnum = ANY(c.conkey)
WHERE c.contype = 'f'
  AND NOT EXISTS (
    SELECT 1 FROM pg_index i
    WHERE i.indrelid = c.conrelid AND a.attnum = ANY(i.indkey)
  );

-- Find slow queries
-- 查找慢查询
SELECT query, mean_exec_time, calls
FROM pg_stat_statements
WHERE mean_exec_time > 100
ORDER BY mean_exec_time DESC;

-- Check table bloat
-- 检查表膨胀
SELECT relname, n_dead_tup, last_vacuum
FROM pg_stat_user_tables
WHERE n_dead_tup > 1000
ORDER BY n_dead_tup DESC;
```

### Configuration Template

### Configuration Template
### 配置模板

```sql
-- Connection limits (adjust for RAM)
-- 连接限制（根据内存调整）
ALTER SYSTEM SET max_connections = 100;
ALTER SYSTEM SET work_mem = '8MB';

-- Timeouts
-- 超时
ALTER SYSTEM SET idle_in_transaction_session_timeout = '30s';
ALTER SYSTEM SET statement_timeout = '30s';

-- Monitoring
-- 监控
CREATE EXTENSION IF NOT EXISTS pg_stat_statements;

-- Security defaults
-- 安全默认值
REVOKE ALL ON SCHEMA public FROM public;

SELECT pg_reload_conf();
```

## Related

## Related
## 相关内容

- Agent: `database-reviewer` - Full database review workflow / 完整的数据库审查工作流
- Skill: `clickhouse-io` - ClickHouse analytics patterns / ClickHouse 分析模式
- Skill: `backend-patterns` - API and backend patterns / API 和后端模式

---

*Based on Supabase Agent Skills (credit: Supabase team) (MIT License)*
