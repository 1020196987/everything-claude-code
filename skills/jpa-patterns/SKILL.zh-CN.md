---
name: jpa-patterns
description: JPA/Hibernate patterns for entity design, relationships, query optimization, transactions, auditing, indexing, pagination, and pooling in Spring Boot.
description zh-CN: Spring Boot 中 JPA/Hibernate 的实体设计、关系、查询优化、事务、审计、索引、分页和连接池模式。
origin: ECC
---

# JPA/Hibernate Patterns
# JPA/Hibernate 模式

Use for data modeling, repositories, and performance tuning in Spring Boot.
用于 Spring Boot 中的数据建模、仓库管理和性能调优。

## When to Activate
## 何时激活

- Designing JPA entities and table mappings
  - 设计 JPA 实体和表映射
- Defining relationships (@OneToMany, @ManyToOne, @ManyToMany)
  - 定义关系（@OneToMany、@ManyToOne、@ManyToMany）
- Optimizing queries (N+1 prevention, fetch strategies, projections)
  - 优化查询（N+1 预防、抓取策略、投影）
- Configuring transactions, auditing, or soft deletes
  - 配置事务、审计或软删除
- Setting up pagination, sorting, or custom repository methods
  - 设置分页、排序或自定义仓库方法
- Tuning connection pooling (HikariCP) or second-level caching
  - 调优连接池（HikariCP）或二级缓存

## Entity Design
## 实体设计

```java
@Entity
@Table(name = "markets", indexes = {
  @Index(name = "idx_markets_slug", columnList = "slug", unique = true)
})
@EntityListeners(AuditingEntityListener.class)
public class MarketEntity {
  @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
  private Long id;

  @Column(nullable = false, length = 200)
  private String name;

  @Column(nullable = false, unique = true, length = 120)
  private String slug;

  @Enumerated(EnumType.STRING)
  private MarketStatus status = MarketStatus.ACTIVE;

  @CreatedDate private Instant createdAt;
  @LastModifiedDate private Instant updatedAt;
}
```

@Enable auditing:
启用审计：

```java
@Configuration
@EnableJpaAuditing
class JpaConfig {}
```

## Relationships and N+1 Prevention
## 关系和 N+1 预防

```java
@OneToMany(mappedBy = "market", cascade = CascadeType.ALL, orphanRemoval = true)
private List<PositionEntity> positions = new ArrayList<>();
```

- Default to lazy loading; use `JOIN FETCH` in queries when needed
  - 默认为延迟加载；需要时在查询中使用 `JOIN FETCH`
- Avoid `EAGER` on collections; use DTO projections for read paths
  - 避免在集合上使用 `EAGER`；对读取路径使用 DTO 投影

```java
@Query("select m from MarketEntity m left join fetch m.positions where m.id = :id")
Optional<MarketEntity> findWithPositions(@Param("id") Long id);
```

## Repository Patterns
## 仓库模式

```java
public interface MarketRepository extends JpaRepository<MarketEntity, Long> {
  Optional<MarketEntity> findBySlug(String slug);

  @Query("select m from MarketEntity m where m.status = :status")
  Page<MarketEntity> findByStatus(@Param("status") MarketStatus status, Pageable pageable);
}
```

- Use projections for lightweight queries:
  - 使用投影进行轻量级查询：

```java
public interface MarketSummary {
  Long getId();
  String getName();
  MarketStatus getStatus();
}
Page<MarketSummary> findAllBy(Pageable pageable);
```

## Transactions
## 事务

- Annotate service methods with `@Transactional`
  - 用 `@Transactional` 注解服务方法
- Use `@Transactional(readOnly = true)` for read paths to optimize
  - 对读取路径使用 `@Transactional(readOnly = true)` 进行优化
- Choose propagation carefully; avoid long-running transactions
  - 仔细选择传播方式；避免长时间运行的事务

```java
@Transactional
public Market updateStatus(Long id, MarketStatus status) {
  MarketEntity entity = repo.findById(id)
      .orElseThrow(() -> new EntityNotFoundException("Market"));
  entity.setStatus(status);
  return Market.from(entity);
}
```

## Pagination
## 分页

```java
PageRequest page = PageRequest.of(pageNumber, pageSize, Sort.by("createdAt").descending());
Page<MarketEntity> markets = repo.findByStatus(MarketStatus.ACTIVE, page);
```

For cursor-like pagination, include `id > :lastId` in JPQL with ordering.
对于类似游标的分页，在 JPQL 中包含 `id > :lastId` 并排序。

## Indexing and Performance
## 索引和性能

- Add indexes for common filters (`status`, `slug`, foreign keys)
  - 为常见过滤器添加索引（`status`、`slug`、外键）
- Use composite indexes matching query patterns (`status, created_at`)
  - 使用与查询模式匹配的多列索引（`status, created_at`）
- Avoid `select *`; project only needed columns
  - 避免 `select *`；只投影需要的列
- Batch writes with `saveAll` and `hibernate.jdbc.batch_size`
  - 使用 `saveAll` 和 `hibernate.jdbc.batch_size` 批量写入

## Connection Pooling (HikariCP)
## 连接池（HikariCP）

Recommended properties:
推荐属性：

```
spring.datasource.hikari.maximum-pool-size=20
spring.datasource.hikari.minimum-idle=5
spring.datasource.hikari.connection-timeout=30000
spring.datasource.hikari.validation-timeout=5000
```

For PostgreSQL LOB handling, add:
对于 PostgreSQL LOB 处理，添加：

```
spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true
```

## Caching
## 缓存

- 1st-level cache is per EntityManager; avoid keeping entities across transactions
  - 一级缓存是每个 EntityManager；避免跨事务保留实体
- For read-heavy entities, consider second-level cache cautiously; validate eviction strategy
  - 对于读取密集型实体，谨慎考虑二级缓存；验证淘汰策略

## Migrations
## 迁移

- Use Flyway or Liquibase; never rely on Hibernate auto DDL in production
  - 使用 Flyway 或 Liquibase；绝不依赖生产中的 Hibernate 自动 DDL
- Keep migrations idempotent and additive; avoid dropping columns without plan
  - 保持迁移幂等且增量；避免无计划地删除列

## Testing Data Access
## 测试数据访问

- Prefer `@DataJpaTest` with Testcontainers to mirror production
  - 优先使用带 Testcontainers 的 `@DataJpaTest` 来镜像生产环境
- Assert SQL efficiency using logs: set `logging.level.org.hibernate.SQL=DEBUG` and `logging.level.org.hibernate.orm.jdbc.bind=TRACE` for parameter values
  - 使用日志断言 SQL 效率：设置 `logging.level.org.hibernate.SQL=DEBUG` 和 `logging.level.org.hibernate.orm.jdbc.bind=TRACE` 以查看参数值

**Remember**: Keep entities lean, queries intentional, and transactions short. Prevent N+1 with fetch strategies and projections, and index for your read/write paths.
**记住**：保持实体精简、查询有意、事务简短。用抓取策略和投影防止 N+1，并为你的读写路径建立索引。
