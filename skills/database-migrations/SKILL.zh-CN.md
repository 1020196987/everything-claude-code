---
name: database-migrations
description: Database migration best practices for schema changes, data migrations, rollbacks, and zero-downtime deployments across PostgreSQL, MySQL, and common ORMs (Prisma, Drizzle, Django, TypeORM, golang-migrate).
description zh-CN: 跨 PostgreSQL、MySQL 和常见 ORM（Prisma、Drizzle、Django、TypeORM、golang-migrate）的数据库迁移最佳实践，包括模式变更、数据迁移、回滚和零停机部署。
origin: ECC
---

# Database Migration Patterns
# Database Migration Patterns
# 数据库迁移模式

Safe, reversible database schema changes for production systems.
## Safe, reversible database schema changes for production systems.
## 生产系统安全、可逆的数据库模式变更。

## When to Activate
## When to Activate
## 何时激活

- Creating or altering database tables
  - 创建或修改数据库表
- Adding/removing columns or indexes
  - 添加/删除列或索引
- Running data migrations (backfill, transform)
  - 运行数据迁移（回填、转换）
- Planning zero-downtime schema changes
  - 规划零停机模式变更
- Setting up migration tooling for a new project
  - 为新项目设置迁移工具

## Core Principles
## Core Principles
## 核心原则

1. **Every change is a migration** — never alter production databases manually
   - **每个变更都是一次迁移**——绝不手动修改生产数据库
2. **Migrations are forward-only in production** — rollbacks use new forward migrations
   - **迁移在生产中是仅向前**——回滚使用新的向前迁移
3. **Schema and data migrations are separate** — never mix DDL and DML in one migration
   - **模式和数据迁移是分开的**——绝不在一个迁移中混合 DDL 和 DML
4. **Test migrations against production-sized data** — a migration that works on 100 rows may lock on 10M
   - **用生产规模的数据测试迁移**——在 100 行上工作的迁移可能在 1000 万行上锁定
5. **Migrations are immutable once deployed** — never edit a migration that has run in production
   - **迁移一旦部署就不可变**——绝不编辑已在生产中运行的迁移

## Migration Safety Checklist
## Migration Safety Checklist
## 迁移安全清单

Before applying any migration:
## Before applying any migration.
## 应用任何迁移之前：

- [ ] Migration has both UP and DOWN (or is explicitly marked irreversible)
  - [ ] 迁移同时具有 UP 和 DOWN（或明确标记为不可逆）
- [ ] No full table locks on large tables (use concurrent operations)
  - [ ] 大表上没有全表锁（使用并发操作）
- [ ] New columns have defaults or are nullable (never add NOT NULL without default)
  - [ ] 新列有默认值或可为空（绝不添加没有默认值的 NOT NULL）
- [ ] Indexes created concurrently (not inline with CREATE TABLE for existing tables)
  - [ ] 并发创建索引（对现有表不是内联在 CREATE TABLE 中）
- [ ] Data backfill is a separate migration from schema change
  - [ ] 数据回填是与模式变更分开的迁移
- [ ] Tested against a copy of production data
  - [ ] 针对生产数据副本进行了测试
- [ ] Rollback plan documented
  - [ ] 回滚计划已记录

## PostgreSQL Patterns
## PostgreSQL Patterns
## PostgreSQL 模式

### Adding a Column Safely
### Adding a Column Safely
### 安全添加列

```sql
-- GOOD: Nullable column, no lock
-- 正确：可空列，无锁
ALTER TABLE users ADD COLUMN avatar_url TEXT;

-- GOOD: Column with default (Postgres 11+ is instant, no rewrite)
-- 正确：带默认值的列（Postgres 11+ 即时完成，无需重写）
ALTER TABLE users ADD COLUMN is_active BOOLEAN NOT NULL DEFAULT true;

-- BAD: NOT NULL without default on existing table (requires full rewrite)
-- 错误：在现有表上没有默认值添加 NOT NULL（需要完全重写）
ALTER TABLE users ADD COLUMN role TEXT NOT NULL;
-- This locks the table and rewrites every row
-- 这会锁定表并重写每一行
```

### Adding an Index Without Downtime
### Adding an Index Without Downtime
### 无停机添加索引

```sql
-- BAD: Blocks writes on large tables
-- 错误：在大型表上阻塞写入
CREATE INDEX idx_users_email ON users (email);

-- GOOD: Non-blocking, allows concurrent writes
-- 正确：无阻塞，允许并发写入
CREATE INDEX CONCURRENTLY idx_users_email ON users (email);

-- Note: CONCURRENTLY cannot run inside a transaction block
-- 注意：CONCURRENTLY 不能在事务块内运行
-- Most migration tools need special handling for this
-- 大多数迁移工具需要特殊处理
```

### Renaming a Column (Zero-Downtime)
### Renaming a Column (Zero-Downtime)
### 重命名列（零停机）

Never rename directly in production. Use the expand-contract pattern:
## Never rename directly in production. Use the expand-contract pattern.
## 绝不要在生产中直接重命名。使用扩展-收缩模式：

```sql
-- Step 1: Add new column (migration 001)
-- 步骤 1：添加新列（迁移 001）
ALTER TABLE users ADD COLUMN display_name TEXT;

-- Step 2: Backfill data (migration 002, data migration)
-- 步骤 2：回填数据（迁移 002，数据迁移）
UPDATE users SET display_name = username WHERE display_name IS NULL;

-- Step 3: Update application code to read/write both columns
-- Deploy application changes
-- 步骤 3：更新应用程序代码以读写两列
-- 部署应用程序更改

-- Step 4: Stop writing to old column, drop it (migration 003)
-- 步骤 4：停止写入旧列，删除它（迁移 003）
ALTER TABLE users DROP COLUMN username;
```

### Removing a Column Safely
### Removing a Column Safely
### 安全删除列

```sql
-- Step 1: Remove all application references to the column
-- 步骤 1：删除对该列的所有应用程序引用
-- Step 2: Deploy application without the column reference
-- 步骤 2：部署不带列引用的应用程序
-- Step 3: Drop column in next migration
-- 步骤 3：在下次迁移中删除列
ALTER TABLE orders DROP COLUMN legacy_status;

-- For Django: use SeparateDatabaseAndState to remove from model
-- without generating DROP COLUMN (then drop in next migration)
-- 对于 Django：使用 SeparateDatabaseAndState 从模型中删除
-- 而不生成 DROP COLUMN（然后在下次迁移中删除）
```

### Large Data Migrations
### Large Data Migrations
### 大型数据迁移

```sql
-- BAD: Updates all rows in one transaction (locks table)
-- 错误：在一个事务中更新所有行（锁定表）
UPDATE users SET normalized_email = LOWER(email);

-- GOOD: Batch update with progress
-- 正确：带进度的批量更新
DO $$
DECLARE
  batch_size INT := 10000;
  rows_updated INT;
BEGIN
  LOOP
    UPDATE users
    SET normalized_email = LOWER(email)
    WHERE id IN (
      SELECT id FROM users
      WHERE normalized_email IS NULL
      LIMIT batch_size
      FOR UPDATE SKIP LOCKED
    );
    GET DIAGNOSTICS rows_updated = ROW_COUNT;
    RAISE NOTICE 'Updated % rows', rows_updated;
    EXIT WHEN rows_updated = 0;
    COMMIT;
  END LOOP;
END $$;
```

## Prisma (TypeScript/Node.js)
## Prisma (TypeScript/Node.js)
## Prisma（TypeScript/Node.js）

### Workflow
### Workflow
### 工作流

```bash
# Create migration from schema changes
# 从模式变更创建迁移
npx prisma migrate dev --name add_user_avatar

# Apply pending migrations in production
# 在生产中应用待处理的迁移
npx prisma migrate deploy

# Reset database (dev only)
# 重置数据库（仅开发）
npx prisma migrate reset

# Generate client after schema changes
# 在模式变更后生成客户端
npx prisma generate
```

### Schema Example
### Schema Example
### 模式示例

```prisma
model User {
  id        String   @id @default(cuid())
  email     String   @unique
  name      String?
  avatarUrl String?  @map("avatar_url")
  createdAt DateTime @default(now()) @map("created_at")
  updatedAt DateTime @updatedAt @map("updated_at")
  orders    Order[]

  @@map("users")
  @@index([email])
}
```

### Custom SQL Migration
### Custom SQL Migration
### 自定义 SQL 迁移

For operations Prisma cannot express (concurrent indexes, data backfills):
## For operations Prisma cannot express (concurrent indexes, data backfills).
## 对于 Prisma 无法表达的操作（并发索引、数据回填）：

```bash
# Create empty migration, then edit the SQL manually
# 创建空迁移，然后手动编辑 SQL
npx prisma migrate dev --create-only --name add_email_index
```

```sql
-- migrations/20240115_add_email_index/migration.sql
-- Prisma cannot generate CONCURRENTLY, so we write it manually
-- Prisma 无法生成 CONCURRENTLY，所以我们手动编写
CREATE INDEX CONCURRENTLY IF NOT EXISTS idx_users_email ON users (email);
```

## Drizzle (TypeScript/Node.js)
## Drizzle (TypeScript/Node.js)
## Drizzle（TypeScript/Node.js）

### Workflow
### Workflow
### 工作流

```bash
# Generate migration from schema changes
# 从模式变更生成迁移
npx drizzle-kit generate

# Apply migrations
# 应用迁移
npx drizzle-kit migrate

# Push schema directly (dev only, no migration file)
# 直接推送模式（仅开发，无迁移文件）
npx drizzle-kit push
```

### Schema Example
### Schema Example
### 模式示例

```typescript
import { pgTable, text, timestamp, uuid, boolean } from "drizzle-orm/pg-core";

export const users = pgTable("users", {
  id: uuid("id").primaryKey().defaultRandom(),
  email: text("email").notNull().unique(),
  name: text("name"),
  isActive: boolean("is_active").notNull().default(true),
  createdAt: timestamp("created_at").notNull().defaultNow(),
  updatedAt: timestamp("updated_at").notNull().defaultNow(),
});
```

## Django (Python)
## Django (Python)
## Django（Python）

### Workflow
### Workflow
### 工作流

```bash
# Generate migration from model changes
# 从模型变更生成迁移
python manage.py makemigrations

# Apply migrations
# 应用迁移
python manage.py migrate

# Show migration status
# 显示迁移状态
python manage.py showmigrations

# Generate empty migration for custom SQL
# 为自定义 SQL 生成空迁移
python manage.py makemigrations --empty app_name -n description
```

### Data Migration
### Data Migration
### 数据迁移

```python
from django.db import migrations

def backfill_display_names(apps, schema_editor):
    User = apps.get_model("accounts", "User")
    batch_size = 5000
    users = User.objects.filter(display_name="")
    while users.exists():
        batch = list(users[:batch_size])
        for user in batch:
            user.display_name = user.username
        User.objects.bulk_update(batch, ["display_name"], batch_size=batch_size)

def reverse_backfill(apps, schema_editor):
    pass  # Data migration, no reverse needed

class Migration(migrations.Migration):
    dependencies = [("accounts", "0015_add_display_name")]

    operations = [
        migrations.RunPython(backfill_display_names, reverse_backfill),
    ]
```

### SeparateDatabaseAndState
### SeparateDatabaseAndState
### SeparateDatabaseAndState

Remove a column from the Django model without dropping it from the database immediately:
## Remove a column from the Django model without dropping it from the database immediately.
## 从 Django 模型中删除列，但不立即从数据库中删除：

```python
class Migration(migrations.Migration):
    operations = [
        migrations.SeparateDatabaseAndState(
            state_operations=[
                migrations.RemoveField(model_name="user", name="legacy_field"),
            ],
            database_operations=[],  # Don't touch the DB yet
        ),
    ]
```

## golang-migrate (Go)
## golang-migrate (Go)
## golang-migrate（Go）

### Workflow
### Workflow
### 工作流

```bash
# Create migration pair
# 创建迁移对
migrate create -ext sql -dir migrations -seq add_user_avatar

# Apply all pending migrations
# 应用所有待处理的迁移
migrate -path migrations -database "$DATABASE_URL" up

# Rollback last migration
# 回滚上次迁移
migrate -path migrations -database "$DATABASE_URL" down 1

# Force version (fix dirty state)
# 强制版本（修复脏状态）
migrate -path migrations -database "$DATABASE_URL" force VERSION
```

### Migration Files
### Migration Files
### 迁移文件

```sql
-- migrations/000003_add_user_avatar.up.sql
ALTER TABLE users ADD COLUMN avatar_url TEXT;
CREATE INDEX CONCURRENTLY idx_users_avatar ON users (avatar_url) WHERE avatar_url IS NOT NULL;

-- migrations/000003_add_user_avatar.down.sql
DROP INDEX IF EXISTS idx_users_avatar;
ALTER TABLE users DROP COLUMN IF EXISTS avatar_url;
```

## Zero-Downtime Migration Strategy
## Zero-Downtime Migration Strategy
## 零停机迁移策略

For critical production changes, follow the expand-contract pattern:
## For critical production changes, follow the expand-contract pattern.
## 对于关键的生产变更，遵循扩展-收缩模式：

```
Phase 1: EXPAND
  - Add new column/table (nullable or with default)
  - Deploy: app writes to BOTH old and new
  - Backfill existing data

Phase 2: MIGRATE
  - Deploy: app reads from NEW, writes to BOTH
  - Verify data consistency

Phase 3: CONTRACT
  - Deploy: app only uses NEW
  - Drop old column/table in separate migration
```
```
阶段 1：扩展
  - 添加新列/表（可空或带默认值）
  - 部署：app 写入新旧两处
  - 回填现有数据

阶段 2：迁移
  - 部署：app 从新读取，写入新旧两处
  - 验证数据一致性

阶段 3：收缩
  - 部署：app 仅使用新
  - 在单独的迁移中删除旧列/表
```

### Timeline Example
### Timeline Example
### 时间表示例

```
Day 1: Migration adds new_status column (nullable)
Day 1: Deploy app v2 — writes to both status and new_status
Day 2: Run backfill migration for existing rows
Day 3: Deploy app v3 — reads from new_status only
Day 7: Migration drops old status column
```
```
第 1 天：迁移添加 new_status 列（可空）
第 1 天：部署 app v2 — 写入 status 和 new_status 两处
第 2 天：运行现有行的回填迁移
第 3 天：部署 app v3 — 仅从 new_status 读取
第 7 天：迁移删除旧的 status 列
```

## Anti-Patterns
## Anti-Patterns
## 反模式

| Anti-Pattern | Why It Fails | Better Approach |
|-------------|-------------|-----------------|
| Manual SQL in production | No audit trail, unrepeatable | Always use migration files |
| 在生产中手动 SQL | 无审计跟踪，不可重复 | 始终使用迁移文件 |
| Editing deployed migrations | Causes drift between environments | Create new migration instead |
| 编辑已部署的迁移 | 导致环境之间漂移 | 改为创建新迁移 |
| NOT NULL without default | Locks table, rewrites all rows | Add nullable, backfill, then add constraint |
| 没有默认值的 NOT NULL | 锁定表，重写所有行 | 添加可空、回填、然后添加约束 |
| Inline index on large table | Blocks writes during build | CREATE INDEX CONCURRENTLY |
| 大表上的内联索引 | 在构建期间阻塞写入 | CREATE INDEX CONCURRENTLY |
| Schema + data in one migration | Hard to rollback, long transactions | Separate migrations |
| 在一个迁移中混合模式和数据 | 难以回滚，长事务 | 分离迁移 |
| Dropping column before removing code | Application errors on missing column | Remove code first, drop column next deploy |
| 在删除代码前删除列 | 应用程序因缺失列而错误 | 先删除代码，下次部署再删除列 |
