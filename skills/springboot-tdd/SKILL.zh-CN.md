---
name: springboot-tdd
description: Test-driven development for Spring Boot using JUnit 5, Mockito, MockMvc, Testcontainers, and JaCoCo. Use when adding features, fixing bugs, or refactoring.
description zh-CN: 使用JUnit 5、Mockito、MockMvc、Testcontainers和JaCoCo进行Spring Boot测试驱动开发。添加功能、修复bug或重构时使用。
origin: ECC
---

# Spring Boot TDD Workflow

## English

TDD guidance for Spring Boot services with 80%+ coverage (unit + integration).

## 中文

Spring Boot服务的TDD指导，要求80%以上覆盖率（单元+集成）。

## When to Use

## English

- New features or endpoints
- Bug fixes or refactors
- Adding data access logic or security rules

## 中文

- 新功能或端点
- Bug修复或重构
- 添加数据访问逻辑或安全规则

## Workflow / 工作流

1) Write tests first (they should fail)
1) 先写测试（应该失败）
2) Implement minimal code to pass
2) 实现最少的代码使测试通过
3) Refactor with tests green
3) 在测试通过时重构
4) Enforce coverage (JaCoCo)
4) 强制覆盖率（JaCoCo）

## Unit Tests (JUnit 5 + Mockito) / 单元测试（JUnit 5 + Mockito）

```java
@ExtendWith(MockitoExtension.class)
class MarketServiceTest {
  @Mock MarketRepository repo;
  @InjectMocks MarketService service;

  @Test
  void createsMarket() {
    CreateMarketRequest req = new CreateMarketRequest("name", "desc", Instant.now(), List.of("cat"));
    when(repo.save(any())).thenAnswer(inv -> inv.getArgument(0));

    Market result = service.create(req);

    assertThat(result.name()).isEqualTo("name");
    verify(repo).save(any());
  }
}
```

Patterns:
- Arrange-Act-Assert
- Avoid partial mocks; prefer explicit stubbing
- Use `@ParameterizedTest` for variants

模式：
- Arrange-Act-Assert
- 避免部分模拟；优先显式存根
- 对变体使用`@ParameterizedTest`

## Web Layer Tests (MockMvc) / Web层测试（MockMvc）

```java
@WebMvcTest(MarketController.class)
class MarketControllerTest {
  @Autowired MockMvc mockMvc;
  @MockBean MarketService marketService;

  @Test
  void returnsMarkets() throws Exception {
    when(marketService.list(any())).thenReturn(Page.empty());

    mockMvc.perform(get("/api/markets"))
        .andExpect(status().isOk())
        .andExpect(jsonPath("$.content").isArray());
  }
}
```

## Integration Tests (SpringBootTest) / 集成测试（SpringBootTest）

```java
@SpringBootTest
@AutoConfigureMockMvc
@ActiveProfiles("test")
class MarketIntegrationTest {
  @Autowired MockMvc mockMvc;

  @Test
  void createsMarket() throws Exception {
    mockMvc.perform(post("/api/markets")
        .contentType(MediaType.APPLICATION_JSON)
        .content("""
          {"name":"Test","description":"Desc","endDate":"2030-01-01T00:00:00Z","categories":["general"]}
        """))
      .andExpect(status().isCreated());
  }
}
```

## Persistence Tests (DataJpaTest) / 持久化测试（DataJpaTest）

```java
@DataJpaTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
@Import(TestContainersConfig.class)
class MarketRepositoryTest {
  @Autowired MarketRepository repo;

  @Test
  void savesAndFinds() {
    MarketEntity entity = new MarketEntity();
    entity.setName("Test");
    repo.save(entity);

    Optional<MarketEntity> found = repo.findByName("Test");
    assertThat(found).isPresent();
  }
}
```

## Testcontainers / Testcontainers

- Use reusable containers for Postgres/Redis to mirror production
- 使用可重用的Postgres/Redis容器来镜像生产
- Wire via `@DynamicPropertySource` to inject JDBC URLs into Spring context
- 通过`@DynamicPropertySource`连接以将JDBC URL注入Spring上下文

## Coverage (JaCoCo) / 覆盖率（JaCoCo）

Maven snippet:
Maven片段：

```xml
<plugin>
  <groupId>org.jacoco</groupId>
  <artifactId>jacoco-maven-plugin</artifactId>
  <version>0.8.14</version>
  <executions>
    <execution>
      <goals><goal>prepare-agent</goal></goals>
    </execution>
    <execution>
      <id>report</id>
      <phase>verify</phase>
      <goals><goal>report</goal></goals>
    </execution>
  </executions>
</plugin>
```

## Assertions / 断言

- Prefer AssertJ (`assertThat`) for readability
- 优先使用AssertJ（`assertThat`）以提高可读性
- For JSON responses, use `jsonPath`
- 对于JSON响应，使用`jsonPath`
- For exceptions: `assertThatThrownBy(...)`
- 对于异常：`assertThatThrownBy(...)`

## Test Data Builders / 测试数据构建器

```java
class MarketBuilder {
  private String name = "Test";
  MarketBuilder withName(String name) { this.name = name; return this; }
  Market build() { return new Market(null, name, MarketStatus.ACTIVE); }
}
```

## CI Commands / CI命令

- Maven: `mvn -T 4 test` or `mvn verify`
- Maven: `mvn -T 4 test` 或 `mvn verify`
- Gradle: `./gradlew test jacocoTestReport`
- Gradle: `./gradlew test jacocoTestReport`

**Remember**: Keep tests fast, isolated, and deterministic. Test behavior, not implementation details.

**记住**：保持测试快速、隔离和确定性。测试行为，而不是实现细节。
