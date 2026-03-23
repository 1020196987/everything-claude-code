---
name: springboot-verification
description: "Verification loop for Spring Boot projects: build, static analysis, tests with coverage, security scans, and diff review before release or PR."
description zh-CN: "Spring Boot 项目验证循环：发布或 PR 前的构建、静态分析、带覆盖率的测试、安全扫描和差异审查。"
origin: ECC
---

# Spring Boot Verification Loop

## Spring Boot 验证循环

Run before PRs, after major changes, and pre-deploy.

在 PR 前、重大更改后和预部署时运行。

## When to Activate

## When to Activate
## 何时激活

- Before opening a pull request for a Spring Boot service
  - 为 Spring Boot 服务开启 pull request 前
- After major refactoring or dependency upgrades
  - 重大重构或依赖升级后
- Pre-deployment verification for staging or production
  - 预部署验证（预发布或生产环境）
- Running full build → lint → test → security scan pipeline
  - 运行完整构建 → lint → 测试 → 安全扫描流程
- Validating test coverage meets thresholds
  - 验证测试覆盖率是否达标

## Phase 1: Build

## Phase 1: Build
## 阶段 1：构建

```bash
mvn -T 4 clean verify -DskipTests
# or 或
./gradlew clean assemble -x test
```

If build fails, stop and fix.

如果构建失败，停止并修复。

## Phase 2: Static Analysis

## Phase 2: Static Analysis
## 阶段 2：静态分析

Maven (common plugins):
```bash
mvn -T 4 spotbugs:check pmd:check checkstyle:check
```

Gradle (if configured):
```bash
./gradlew checkstyleMain pmdMain spotbugsMain
```

## Phase 3: Tests + Coverage

## Phase 3: Tests + Coverage
## 阶段 3：测试 + 覆盖率

```bash
mvn -T 4 test
mvn jacoco:report   # verify 80%+ coverage 验证 80%+ 覆盖率
# or 或
./gradlew test jacocoTestReport
```

Report:
- Total tests, passed/failed
- Coverage % (lines/branches)

报告：
- 总测试数、通过/失败数
- 覆盖率 %（行/分支）

### Unit Tests

### Unit Tests
### 单元测试

Test service logic in isolation with mocked dependencies:

用模拟依赖隔离测试服务逻辑：

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {

  @Mock private UserRepository userRepository;
  @InjectMocks private UserService userService;

  @Test
  void createUser_validInput_returnsUser() {
    var dto = new CreateUserDto("Alice", "alice@example.com");
    var expected = new User(1L, "Alice", "alice@example.com");
    when(userRepository.save(any(User.class))).thenReturn(expected);

    var result = userService.create(dto);

    assertThat(result.name()).isEqualTo("Alice");
    verify(userRepository).save(any(User.class));
  }

  @Test
  void createUser_duplicateEmail_throwsException() {
    var dto = new CreateUserDto("Alice", "existing@example.com");
    when(userRepository.existsByEmail(dto.email())).thenReturn(true);

    assertThatThrownBy(() -> userService.create(dto))
        .isInstanceOf(DuplicateEmailException.class);
  }
}
```

### Integration Tests with Testcontainers

### Integration Tests with Testcontainers
### 使用 Testcontainers 进行集成测试

Test against a real database instead of H2:

使用真实数据库而非 H2 进行测试：

```java
@SpringBootTest
@Testcontainers
class UserRepositoryIntegrationTest {

  @Container
  static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:16-alpine")
      .withDatabaseName("testdb");

  @DynamicPropertySource
  static void configureProperties(DynamicPropertyRegistry registry) {
    registry.add("spring.datasource.url", postgres::getJdbcUrl);
    registry.add("spring.datasource.username", postgres::getUsername);
    registry.add("spring.datasource.password", postgres::getPassword);
  }

  @Autowired private UserRepository userRepository;

  @Test
  void findByEmail_existingUser_returnsUser() {
    userRepository.save(new User("Alice", "alice@example.com"));

    var found = userRepository.findByEmail("alice@example.com");

    assertThat(found).isPresent();
    assertThat(found.get().getName()).isEqualTo("Alice");
  }
}
```

### API Tests with MockMvc

### API Tests with MockMvc
### 使用 MockMvc 进行 API 测试

Test controller layer with full Spring context:

使用完整 Spring 上下文测试控制器层：

```java
@WebMvcTest(UserController.class)
class UserControllerTest {

  @Autowired private MockMvc mockMvc;
  @MockBean private UserService userService;

  @Test
  void createUser_validInput_returns201() throws Exception {
    var user = new UserDto(1L, "Alice", "alice@example.com");
    when(userService.create(any())).thenReturn(user);

    mockMvc.perform(post("/api/users")
            .contentType(MediaType.APPLICATION_JSON)
            .content("""
                {"name": "Alice", "email": "alice@example.com"}
                """))
        .andExpect(status().isCreated())
        .andExpect(jsonPath("$.name").value("Alice"));
  }

  @Test
  void createUser_invalidEmail_returns400() throws Exception {
    mockMvc.perform(post("/api/users")
            .contentType(MediaType.APPLICATION_JSON)
            .content("""
                {"name": "Alice", "email": "not-an-email"}
                """))
        .andExpect(status().isBadRequest());
  }
}
```

## Phase 4: Security Scan

## Phase 4: Security Scan
## 阶段 4：安全扫描

```bash
# Dependency CVEs
mvn org.owasp:dependency-check-maven:check
# or 或
./gradlew dependencyCheckAnalyze

# Secrets in source
grep -rn "password\s*=\s*\"" src/ --include="*.java" --include="*.yml" --include="*.properties"
grep -rn "sk-\|api_key\|secret" src/ --include="*.java" --include="*.yml"

# Secrets (git history)
git secrets --scan  # if configured 如果已配置
```

### Common Security Findings

### Common Security Findings
### 常见安全问题

```
# Check for System.out.println (use logger instead)
# 检查 System.out.println（使用 logger）
grep -rn "System\.out\.print" src/main/ --include="*.java"

# Check for raw exception messages in responses
# 检查响应中的原始异常消息
grep -rn "e\.getMessage()" src/main/ --include="*.java"

# Check for wildcard CORS
# 检查通配符 CORS
grep -rn "allowedOrigins.*\*" src/main/ --include="*.java"
```

## Phase 5: Lint/Format (optional gate)

## Phase 5: Lint/Format (optional gate)
## 阶段 5：Lint/格式化（可选门禁）

```bash
mvn spotless:apply   # if using Spotless plugin 如果使用 Spotless 插件
./gradlew spotlessApply
```

## Phase 6: Diff Review

## Phase 6: Diff Review
## 阶段 6：差异审查

```bash
git diff --stat
git diff
```

Checklist:
- No debugging logs left (`System.out`, `log.debug` without guards)
  - 没有留下调试日志（`System.out`、无守卫的 `log.debug`）
- Meaningful errors and HTTP statuses
  - 有意义的错误和 HTTP 状态
- Transactions and validation present where needed
  - 需要时有事务和验证
- Config changes documented
  - 配置更改已记录

## Output Template

## Output Template
## 输出模板

```
VERIFICATION REPORT
===================
Build:     [PASS/FAIL]
Static:    [PASS/FAIL] (spotbugs/pmd/checkstyle)
Tests:     [PASS/FAIL] (X/Y passed, Z% coverage)
Security:  [PASS/FAIL] (CVE findings: N)
Diff:      [X files changed]

Overall:   [READY / NOT READY]

Issues to Fix:
1. ...
2. ...
```

## Continuous Mode

## Continuous Mode
## 持续模式

- Re-run phases on significant changes or every 30–60 minutes in long sessions
  - 在重大更改后或在长会话中每 30-60 分钟重新运行各阶段
- Keep a short loop: `mvn -T 4 test` + spotbugs for quick feedback
  - 保持短循环：`mvn -T 4 test` + spotbugs 快速反馈

**Remember**: Fast feedback beats late surprises. Keep the gate strict—treat warnings as defects in production systems.

**记住**：快速反馈优于后期惊喜。保持门禁严格——在生产系统中将警告视为缺陷。
