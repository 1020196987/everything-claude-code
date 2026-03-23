---
name: clickhouse-io
description: ClickHouse database patterns, query optimization, analytics, and data engineering best practices for high-performance analytical workloads.
description zh-CN: ClickHouse 数据库模式、查询优化、分析及高性能分析工作负载的数据工程最佳实践。
origin: ECC
---

# ClickHouse Analytics Patterns
# ClickHouse 分析模式

ClickHouse-specific patterns for high-performance analytics and data engineering.
针对高性能分析和大数据工程的 ClickHouse 特定模式。

## When to Activate
## 何时激活

- Designing ClickHouse table schemas (MergeTree engine selection)
  - 设计 ClickHouse 表结构（MergeTree 引擎选择）
- Writing analytical queries (aggregations, window functions, joins)
  - 编写分析查询（聚合、窗口函数、连接）
- Optimizing query performance (partition pruning, projections, materialized views)
  - 优化查询性能（分区裁剪、投影、物化视图）
- Ingesting large volumes of data (batch inserts, Kafka integration)
  - 摄入大量数据（批量插入、Kafka 集成）
- Migrating from PostgreSQL/MySQL to ClickHouse for analytics
  - 从 PostgreSQL/MySQL 迁移到 ClickHouse 进行分析
- Implementing real-time dashboards or time-series analytics
  - 实现实时仪表板或时间序列分析

## Overview
## 概述

ClickHouse is a column-oriented database management system (DBMS) for online analytical processing (OLAP). It's optimized for fast analytical queries on large datasets.
ClickHouse 是一个用于在线分析处理（OLAP）的列式数据库管理系统（DBMS）。它针对大型数据集的快速分析查询进行了优化。

**Key Features:**
**关键特性：**
- Column-oriented storage
  - 列式存储
- Data compression
  - 数据压缩
- Parallel query execution
  - 并行查询执行
- Distributed queries
  - 分布式查询
- Real-time analytics
  - 实时分析

## Table Design Patterns
## 表设计模式

### MergeTree Engine (Most Common)
### MergeTree 引擎（最常用）

```sql
CREATE TABLE markets_analytics (
    date Date,
    market_id String,
    market_name String,
    volume UInt64,
    trades UInt32,
    unique_traders UInt32,
    avg_trade_size Float64,
    created_at DateTime
) ENGINE = MergeTree()
PARTITION BY toYYYYMM(date)
ORDER BY (date, market_id)
SETTINGS index_granularity = 8192;
```

### ReplacingMergeTree (Deduplication)
### ReplacingMergeTree（去重）

```sql
-- For data that may have duplicates (e.g., from multiple sources)
-- 用于可能有重复的数据（例如来自多个来源）
CREATE TABLE user_events (
    event_id String,
    user_id String,
    event_type String,
    timestamp DateTime,
    properties String
) ENGINE = ReplacingMergeTree()
PARTITION BY toYYYYMM(timestamp)
ORDER BY (user_id, event_id, timestamp)
PRIMARY KEY (user_id, event_id);
```

### AggregatingMergeTree (Pre-aggregation)
### AggregatingMergeTree（预聚合）

```sql
-- For maintaining aggregated metrics
-- 用于维护聚合指标
CREATE TABLE market_stats_hourly (
    hour DateTime,
    market_id String,
    total_volume AggregateFunction(sum, UInt64),
    total_trades AggregateFunction(count, UInt32),
    unique_users AggregateFunction(uniq, String)
) ENGINE = AggregatingMergeTree()
PARTITION BY toYYYYMM(hour)
ORDER BY (hour, market_id);

-- Query aggregated data
-- 查询聚合数据
SELECT
    hour,
    market_id,
    sumMerge(total_volume) AS volume,
    countMerge(total_trades) AS trades,
    uniqMerge(unique_users) AS users
FROM market_stats_hourly
WHERE hour >= toStartOfHour(now() - INTERVAL 24 HOUR)
GROUP BY hour, market_id
ORDER BY hour DESC;
```

## Query Optimization Patterns
## 查询优化模式

### Efficient Filtering
### 高效过滤

```sql
-- ✅ GOOD: Use indexed columns first
-- ✅ 好：优先使用有索引的列
SELECT *
FROM markets_analytics
WHERE date >= '2025-01-01'
  AND market_id = 'market-123'
  AND volume > 1000
ORDER BY date DESC
LIMIT 100;

-- ❌ BAD: Filter on non-indexed columns first
-- ❌ 差：优先过滤无索引的列
SELECT *
FROM markets_analytics
WHERE volume > 1000
  AND market_name LIKE '%election%'
  AND date >= '2025-01-01';
```

### Aggregations
### 聚合

```sql
-- ✅ GOOD: Use ClickHouse-specific aggregation functions
-- ✅ 好：使用 ClickHouse 特定的聚合函数
SELECT
    toStartOfDay(created_at) AS day,
    market_id,
    sum(volume) AS total_volume,
    count() AS total_trades,
    uniq(trader_id) AS unique_traders,
    avg(trade_size) AS avg_size
FROM trades
WHERE created_at >= today() - INTERVAL 7 DAY
GROUP BY day, market_id
ORDER BY day DESC, total_volume DESC;

-- ✅ Use quantile for percentiles (more efficient than percentile)
-- ✅ 使用 quantile 计算百分位数（比 percentile 更高效）
SELECT
    quantile(0.50)(trade_size) AS median,
    quantile(0.95)(trade_size) AS p95,
    quantile(0.99)(trade_size) AS p99
FROM trades
WHERE created_at >= now() - INTERVAL 1 HOUR;
```

### Window Functions
### 窗口函数

```sql
-- Calculate running totals
-- 计算累计总和
SELECT
    date,
    market_id,
    volume,
    sum(volume) OVER (
        PARTITION BY market_id
        ORDER BY date
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS cumulative_volume
FROM markets_analytics
WHERE date >= today() - INTERVAL 30 DAY
ORDER BY market_id, date;
```

## Data Insertion Patterns
## 数据插入模式

### Bulk Insert (Recommended)
### 批量插入（推荐）

```typescript
import { ClickHouse } from 'clickhouse'

const clickhouse = new ClickHouse({
  url: process.env.CLICKHOUSE_URL,
  port: 8123,
  basicAuth: {
    username: process.env.CLICKHOUSE_USER,
    password: process.env.CLICKHOUSE_PASSWORD
  }
})

// ✅ Batch insert (efficient)
// ✅ 批量插入（高效）
async function bulkInsertTrades(trades: Trade[]) {
  const values = trades.map(trade => `(
    '${trade.id}',
    '${trade.market_id}',
    '${trade.user_id}',
    ${trade.amount},
    '${trade.timestamp.toISOString()}'
  )`).join(',')

  await clickhouse.query(`
    INSERT INTO trades (id, market_id, user_id, amount, timestamp)
    VALUES ${values}
  `).toPromise()
}

// ❌ Individual inserts (slow)
// ❌ 单条插入（慢）
async function insertTrade(trade: Trade) {
  // Don't do this in a loop!
  // 不要在循环中这样做！
  await clickhouse.query(`
    INSERT INTO trades VALUES ('${trade.id}', ...)
  `).toPromise()
}
```

### Streaming Insert
### 流式插入

```typescript
// For continuous data ingestion
// 用于持续数据摄入
import { createWriteStream } from 'fs'
import { pipeline } from 'stream/promises'

async function streamInserts() {
  const stream = clickhouse.insert('trades').stream()

  for await (const batch of dataSource) {
    stream.write(batch)
  }

  await stream.end()
}
```

## Materialized Views
## 物化视图

### Real-time Aggregations
### 实时聚合

```sql
-- Create materialized view for hourly stats
-- 创建每小时统计的物化视图
CREATE MATERIALIZED VIEW market_stats_hourly_mv
TO market_stats_hourly
AS SELECT
    toStartOfHour(timestamp) AS hour,
    market_id,
    sumState(amount) AS total_volume,
    countState() AS total_trades,
    uniqState(user_id) AS unique_users
FROM trades
GROUP BY hour, market_id;

-- Query the materialized view
-- 查询物化视图
SELECT
    hour,
    market_id,
    sumMerge(total_volume) AS volume,
    countMerge(total_trades) AS trades,
    uniqMerge(unique_users) AS users
FROM market_stats_hourly
WHERE hour >= now() - INTERVAL 24 HOUR
GROUP BY hour, market_id;
```

## Performance Monitoring
## 性能监控

### Query Performance
### 查询性能

```sql
-- Check slow queries
-- 检查慢查询
SELECT
    query_id,
    user,
    query,
    query_duration_ms,
    read_rows,
    read_bytes,
    memory_usage
FROM system.query_log
WHERE type = 'QueryFinish'
  AND query_duration_ms > 1000
  AND event_time >= now() - INTERVAL 1 HOUR
ORDER BY query_duration_ms DESC
LIMIT 10;
```

### Table Statistics
### 表统计

```sql
-- Check table sizes
-- 检查表大小
SELECT
    database,
    table,
    formatReadableSize(sum(bytes)) AS size,
    sum(rows) AS rows,
    max(modification_time) AS latest_modification
FROM system.parts
WHERE active
GROUP BY database, table
ORDER BY sum(bytes) DESC;
```

## Common Analytics Queries
## 常用分析查询

### Time Series Analysis
### 时间序列分析

```sql
-- Daily active users
-- 每日活跃用户
SELECT
    toDate(timestamp) AS date,
    uniq(user_id) AS daily_active_users
FROM events
WHERE timestamp >= today() - INTERVAL 30 DAY
GROUP BY date
ORDER BY date;

-- Retention analysis
-- 留存分析
SELECT
    signup_date,
    countIf(days_since_signup = 0) AS day_0,
    countIf(days_since_signup = 1) AS day_1,
    countIf(days_since_signup = 7) AS day_7,
    countIf(days_since_signup = 30) AS day_30
FROM (
    SELECT
        user_id,
        min(toDate(timestamp)) AS signup_date,
        toDate(timestamp) AS activity_date,
        dateDiff('day', signup_date, activity_date) AS days_since_signup
    FROM events
    GROUP BY user_id, activity_date
)
GROUP BY signup_date
ORDER BY signup_date DESC;
```

### Funnel Analysis
### 漏斗分析

```sql
-- Conversion funnel
-- 转化漏斗
SELECT
    countIf(step = 'viewed_market') AS viewed,
    countIf(step = 'clicked_trade') AS clicked,
    countIf(step = 'completed_trade') AS completed,
    round(clicked / viewed * 100, 2) AS view_to_click_rate,
    round(completed / clicked * 100, 2) AS click_to_completion_rate
FROM (
    SELECT
        user_id,
        session_id,
        event_type AS step
    FROM events
    WHERE event_date = today()
)
GROUP BY session_id;
```

### Cohort Analysis
### 队列分析

```sql
-- User cohorts by signup month
-- 按注册月份的用户队列
SELECT
    toStartOfMonth(signup_date) AS cohort,
    toStartOfMonth(activity_date) AS month,
    dateDiff('month', cohort, month) AS months_since_signup,
    count(DISTINCT user_id) AS active_users
FROM (
    SELECT
        user_id,
        min(toDate(timestamp)) OVER (PARTITION BY user_id) AS signup_date,
        toDate(timestamp) AS activity_date
    FROM events
)
GROUP BY cohort, month, months_since_signup
ORDER BY cohort, months_since_signup;
```

## Data Pipeline Patterns
## 数据管道模式

### ETL Pattern
### ETL 模式

```typescript
// Extract, Transform, Load
// 抽取、转换、加载
async function etlPipeline() {
  // 1. Extract from source
  // 1. 从源抽取
  const rawData = await extractFromPostgres()

  // 2. Transform
  // 2. 转换
  const transformed = rawData.map(row => ({
    date: new Date(row.created_at).toISOString().split('T')[0],
    market_id: row.market_slug,
    volume: parseFloat(row.total_volume),
    trades: parseInt(row.trade_count)
  }))

  // 3. Load to ClickHouse
  // 3. 加载到 ClickHouse
  await bulkInsertToClickHouse(transformed)
}

// Run periodically
// 定期运行
setInterval(etlPipeline, 60 * 60 * 1000)  // Every hour
// 每小时
```

### Change Data Capture (CDC)
### 变更数据捕获（CDC）

```typescript
// Listen to PostgreSQL changes and sync to ClickHouse
// 监听 PostgreSQL 变更并同步到 ClickHouse
import { Client } from 'pg'

const pgClient = new Client({ connectionString: process.env.DATABASE_URL })

pgClient.query('LISTEN market_updates')

pgClient.on('notification', async (msg) => {
  const update = JSON.parse(msg.payload)

  await clickhouse.insert('market_updates', [
    {
      market_id: update.id,
      event_type: update.operation,  // INSERT, UPDATE, DELETE
      timestamp: new Date(),
      data: JSON.stringify(update.new_data)
    }
  ])
})
```

## Best Practices
## 最佳实践

### 1. Partitioning Strategy
### 1. 分区策略
- Partition by time (usually month or day)
  - 按时间分区（通常按月或日）
- Avoid too many partitions (performance impact)
  - 避免过多分区（影响性能）
- Use DATE type for partition key
  - 使用 DATE 类型作为分区键

### 2. Ordering Key
### 2. 排序键
- Put most frequently filtered columns first
  - 将最常过滤的列放在前面
- Consider cardinality (high cardinality first)
  - 考虑基数（高基数在前）
- Order impacts compression
  - 排序影响压缩

### 3. Data Types
### 3. 数据类型
- Use smallest appropriate type (UInt32 vs UInt64)
  - 使用最小合适类型（UInt32 vs UInt64）
- Use LowCardinality for repeated strings
  - 对重复字符串使用 LowCardinality
- Use Enum for categorical data
  - 对分类数据使用 Enum

### 4. Avoid
### 4. 避免事项
- SELECT * (specify columns)
  - SELECT *（指定列）
- FINAL (merge data before query instead)
  - FINAL（在查询前合并数据）
- Too many JOINs (denormalize for analytics)
  - 过多 JOIN（为分析反规范化）
- Small frequent inserts (batch instead)
  - 小量频繁插入（改为批量）

### 5. Monitoring
### 5. 监控
- Track query performance
  - 跟踪查询性能
- Monitor disk usage
  - 监控磁盘使用
- Check merge operations
  - 检查合并操作
- Review slow query log
  - 审查慢查询日志

**Remember**: ClickHouse excels at analytical workloads. Design tables for your query patterns, batch inserts, and leverage materialized views for real-time aggregations.
**记住**：ClickHouse 擅长分析工作负载。为查询模式设计表、批量插入，并利用物化视图实现实时聚合。
