---
name: kotlin-testing
description: Kotlin testing patterns with Kotest, MockK, coroutine testing, property-based testing, and Kover coverage. Follows TDD methodology with idiomatic Kotlin practices.
description zh-CN: 使用 Kotest、MockK、协程测试、基于属性的测试和 Kover 覆盖率进行 Kotlin 测试的模式。遵循 TDD 方法论和惯用 Kotlin 实践。
origin: ECC
---

# Kotlin Testing Patterns

## Kotlin Testing Patterns
## Kotlin 测试模式

Comprehensive Kotlin testing patterns for writing reliable, maintainable tests following TDD methodology with Kotest and MockK.
使用 Kotest 和 MockK 编写可靠、可维护测试的综合 Kotlin 测试模式，遵循 TDD 方法论。

## When to Use

## When to Use
## 何时使用

- Writing new Kotlin functions or classes
  - 编写新的 Kotlin 函数或类
- Adding test coverage to existing Kotlin code
  - 为现有 Kotlin 代码添加测试覆盖率
- Implementing property-based tests
  - 实现基于属性的测试
- Following TDD workflow in Kotlin projects
  - 在 Kotlin 项目中遵循 TDD 工作流
- Configuring Kover for code coverage
  - 配置 Kover 进行代码覆盖率检查

## How It Works

## How It Works
## 工作原理

1. **Identify target code** — Find the function, class, or module to test
   - **确定目标代码** — 找到要测试的函数、类或模块
2. **Write a Kotest spec** — Choose a spec style (StringSpec, FunSpec, BehaviorSpec) matching the test scope
   - **编写 Kotest spec** — 选择与测试范围匹配的 spec 风格（StringSpec、FunSpec、BehaviorSpec）
3. **Mock dependencies** — Use MockK to isolate the unit under test
   - **模拟依赖** — 使用 MockK 隔离被测试的单元
4. **Run tests (RED)** — Verify the test fails with the expected error
   - **运行测试（红色）** — 验证测试以预期错误失败
5. **Implement code (GREEN)** — Write minimal code to pass the test
   - **实现代码（绿色）** — 编写最少的代码使测试通过
6. **Refactor** — Improve the implementation while keeping tests green
   - **重构** — 在保持测试通过的情况下改进实现
7. **Check coverage** — Run `./gradlew koverHtmlReport` and verify 80%+ coverage
   - **检查覆盖率** — 运行 `./gradlew koverHtmlReport` 并验证覆盖率 80% 以上

## Examples

## Examples
## 示例

The following sections contain detailed, runnable examples for each testing pattern:
以下各节包含每个测试模式的详细、可运行的示例：

### Quick Reference

### Quick Reference
### 快速参考

- **Kotest specs** — StringSpec, FunSpec, BehaviorSpec, DescribeSpec examples in [Kotest Spec Styles](#kotest-spec-styles)
  - **Kotest specs** — [Kotest Spec 风格](#kotest-spec-styles) 中的 StringSpec、FunSpec、BehaviorSpec、DescribeSpec 示例
- **Mocking** — MockK setup, coroutine mocking, argument capture in [MockK](#mockk)
  - **模拟** — [MockK](#mockk) 中的 MockK 设置、协程模拟、参数捕获
- **TDD walkthrough** — Full RED/GREEN/REFACTOR cycle with EmailValidator in [TDD Workflow for Kotlin](#tdd-workflow-for-kotlin)
  - **TDD 演练** — [Kotlin TDD 工作流](#tdd-workflow-for-kotlin) 中 EmailValidator 的完整 RED/GREEN/REFACTOR 循环
- **Coverage** — Kover configuration and commands in [Kover Coverage](#kover-coverage)
  - **覆盖率** — [Kover 覆盖率](#kover-coverage) 中的 Kover 配置和命令
- **Ktor testing** — testApplication setup in [Ktor testApplication Testing](#ktor-testapplication-testing)
  - **Ktor 测试** — [Ktor testApplication 测试](#ktor-testapplication-testing) 中的 testApplication 设置

### TDD Workflow for Kotlin

### TDD Workflow for Kotlin
### Kotlin TDD 工作流

#### The RED-GREEN-REFACTOR Cycle

#### The RED-GREEN-REFACTOR Cycle
#### 红-绿-重构循环

```
RED     -> Write a failing test first
         -> 首先编写一个失败的测试
GREEN   -> Write minimal code to pass the test
         -> 编写最少的代码使测试通过
REFACTOR -> Improve code while keeping tests green
         -> 在保持测试通过的情况下改进代码
REPEAT  -> Continue with next requirement
         -> 继续下一个需求
```

#### Step-by-Step TDD in Kotlin

#### Step-by-Step TDD in Kotlin
#### Kotlin 分步 TDD

```kotlin
// Step 1: Define the interface/signature
// EmailValidator.kt
package com.example.validator

fun validateEmail(email: String): Result<String> {
    TODO("not implemented")
}

// Step 2: Write failing test (RED)
// EmailValidatorTest.kt
package com.example.validator

import io.kotest.core.spec.style.StringSpec
import io.kotest.matchers.result.shouldBeFailure
import io.kotest.matchers.result.shouldBeSuccess

class EmailValidatorTest : StringSpec({
    "valid email returns success" {
        validateEmail("user@example.com").shouldBeSuccess("user@example.com")
    }

    "empty email returns failure" {
        validateEmail("").shouldBeFailure()
    }

    "email without @ returns failure" {
        validateEmail("userexample.com").shouldBeFailure()
    }
})

// Step 3: Run tests - verify FAIL
// $ ./gradlew test
// EmailValidatorTest > valid email returns success FAILED
//   kotlin.NotImplementedError: An operation is not implemented

// Step 4: Implement minimal code (GREEN)
fun validateEmail(email: String): Result<String> {
    if (email.isBlank()) return Result.failure(IllegalArgumentException("Email cannot be blank"))
    if ('@' !in email) return Result.failure(IllegalArgumentException("Email must contain @"))
    val regex = Regex("^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,}$")
    if (!regex.matches(email)) return Result.failure(IllegalArgumentException("Invalid email format"))
    return Result.success(email)
}

// Step 5: Run tests - verify PASS
// $ ./gradlew test
// EmailValidatorTest > valid email returns success PASSED
// EmailValidatorTest > empty email returns failure PASSED
// EmailValidatorTest > email without @ returns failure PASSED

// Step 6: Refactor if needed, verify tests still pass
```

### Kotest Spec Styles

### Kotest Spec Styles
### Kotest Spec 风格

#### StringSpec (Simplest)

#### StringSpec (Simplest)
#### StringSpec（最简单）

```kotlin
class CalculatorTest : StringSpec({
    "add two positive numbers" {
        Calculator.add(2, 3) shouldBe 5
    }

    "add negative numbers" {
        Calculator.add(-1, -2) shouldBe -3
    }

    "add zero" {
        Calculator.add(0, 5) shouldBe 5
    }
})
```

#### FunSpec (JUnit-like)

#### FunSpec (JUnit-like)
#### FunSpec（类似 JUnit）

```kotlin
class UserServiceTest : FunSpec({
    val repository = mockk<UserRepository>()
    val service = UserService(repository)

    test("getUser returns user when found") {
        val expected = User(id = "1", name = "Alice")
        coEvery { repository.findById("1") } returns expected

        val result = service.getUser("1")

        result shouldBe expected
    }

    test("getUser throws when not found") {
        coEvery { repository.findById("999") } returns null

        shouldThrow<UserNotFoundException> {
            service.getUser("999")
        }
    }
})
```

#### BehaviorSpec (BDD Style)

#### BehaviorSpec (BDD Style)
#### BehaviorSpec（BDD 风格）

```kotlin
class OrderServiceTest : BehaviorSpec({
    val repository = mockk<OrderRepository>()
    val paymentService = mockk<PaymentService>()
    val service = OrderService(repository, paymentService)

    Given("a valid order request") {
        val request = CreateOrderRequest(
            userId = "user-1",
            items = listOf(OrderItem("product-1", quantity = 2)),
        )

        When("the order is placed") {
            coEvery { paymentService.charge(any()) } returns PaymentResult.Success
            coEvery { repository.save(any()) } answers { firstArg() }

            val result = service.placeOrder(request)

            Then("it should return a confirmed order") {
                result.status shouldBe OrderStatus.CONFIRMED
            }

            Then("it should charge payment") {
                coVerify(exactly = 1) { paymentService.charge(any()) }
            }
        }

        When("payment fails") {
            coEvery { paymentService.charge(any()) } returns PaymentResult.Declined

            Then("it should throw PaymentException") {
                shouldThrow<PaymentException> {
                    service.placeOrder(request)
                }
            }
        }
    }
})
```

#### DescribeSpec (RSpec Style)

#### DescribeSpec (RSpec Style)
#### DescribeSpec（RSpec 风格）

```kotlin
class UserValidatorTest : DescribeSpec({
    describe("validateUser") {
        val validator = UserValidator()

        context("with valid input") {
            it("accepts a normal user") {
                val user = CreateUserRequest("Alice", "alice@example.com")
                validator.validate(user).shouldBeValid()
            }
        }

        context("with invalid name") {
            it("rejects blank name") {
                val user = CreateUserRequest("", "alice@example.com")
                validator.validate(user).shouldBeInvalid()
            }

            it("rejects name exceeding max length") {
                val user = CreateUserRequest("A".repeat(256), "alice@example.com")
                validator.validate(user).shouldBeInvalid()
            }
        }
    }
})
```

### Kotest Matchers

### Kotest Matchers
### Kotest 匹配器

#### Core Matchers

#### Core Matchers
#### 核心匹配器

```kotlin
import io.kotest.matchers.shouldBe
import io.kotest.matchers.shouldNotBe
import io.kotest.matchers.string.*
import io.kotest.matchers.collections.*
import io.kotest.matchers.nulls.*

// Equality
// 相等性
result shouldBe expected
result shouldNotBe unexpected

// Strings
// 字符串
name shouldStartWith "Al"
name shouldEndWith "ice"
name shouldContain "lic"
name shouldMatch Regex("[A-Z][a-z]+")
name.shouldBeBlank()

// Collections
// 集合
list shouldContain "item"
list shouldHaveSize 3
list.shouldBeSorted()
list.shouldContainAll("a", "b", "c")
list.shouldBeEmpty()

// Nulls
// 空值
result.shouldNotBeNull()
result.shouldBeNull()

// Types
// 类型
result.shouldBeInstanceOf<User>()

// Numbers
// 数字
count shouldBeGreaterThan 0
price shouldBeInRange 1.0..100.0

// Exceptions
// 异常
shouldThrow<IllegalArgumentException> {
    validateAge(-1)
}.message shouldBe "Age must be positive"

shouldNotThrow<Exception> {
    validateAge(25)
}
```

#### Custom Matchers

#### Custom Matchers
#### 自定义匹配器

```kotlin
fun beActiveUser() = object : Matcher<User> {
    override fun test(value: User) = MatcherResult(
        value.isActive && value.lastLogin != null,
        { "User ${value.id} should be active with a last login" },
        { "User ${value.id} should not be active" },
    )
}

// Usage
// 使用
user should beActiveUser()
```

### MockK

### MockK
### MockK

#### Basic Mocking

#### Basic Mocking
#### 基本模拟

```kotlin
class UserServiceTest : FunSpec({
    val repository = mockk<UserRepository>()
    val logger = mockk<Logger>(relaxed = true) // Relaxed: returns defaults
    val service = UserService(repository, logger)

    beforeTest {
        clearMocks(repository, logger)
    }

    test("findUser delegates to repository") {
        val expected = User(id = "1", name = "Alice")
        every { repository.findById("1") } returns expected

        val result = service.findUser("1")

        result shouldBe expected
        verify(exactly = 1) { repository.findById("1") }
    }

    test("findUser returns null for unknown id") {
        every { repository.findById(any()) } returns null

        val result = service.findUser("unknown")

        result.shouldBeNull()
    }
})
```

#### Coroutine Mocking

#### Coroutine Mocking
#### 协程模拟

```kotlin
class AsyncUserServiceTest : FunSpec({
    val repository = mockk<UserRepository>()
    val service = UserService(repository)

    test("getUser suspending function") {
        coEvery { repository.findById("1") } returns User(id = "1", name = "Alice")

        val result = service.getUser("1")

        result.name shouldBe "Alice"
        coVerify { repository.findById("1") }
    }

    test("getUser with delay") {
        coEvery { repository.findById("1") } coAnswers {
            delay(100) // Simulate async work
            User(id = "1", name = "Alice")
        }

        val result = service.getUser("1")
        result.name shouldBe "Alice"
    }
})
```

#### Argument Capture

#### Argument Capture
#### 参数捕获

```kotlin
test("save captures the user argument") {
    val slot = slot<User>()
    coEvery { repository.save(capture(slot)) } returns Unit

    service.createUser(CreateUserRequest("Alice", "alice@example.com"))

    slot.captured.name shouldBe "Alice"
    slot.captured.email shouldBe "alice@example.com"
    slot.captured.id.shouldNotBeNull()
}
```

#### Spy and Partial Mocking

#### Spy and Partial Mocking
#### Spy 和部分模拟

```kotlin
test("spy on real object") {
    val realService = UserService(repository)
    val spy = spyk(realService)

    every { spy.generateId() } returns "fixed-id"

    spy.createUser(request)

    verify { spy.generateId() } // Overridden
    // Other methods use real implementation
}
```

### Coroutine Testing

### Coroutine Testing
### 协程测试

#### runTest for Suspend Functions

#### runTest for Suspend Functions
#### 挂起函数的 runTest

```kotlin
import kotlinx.coroutines.test.runTest

class CoroutineServiceTest : FunSpec({
    test("concurrent fetches complete together") {
        runTest {
            val service = DataService(testScope = this)

            val result = service.fetchAllData()

            result.users.shouldNotBeEmpty()
            result.products.shouldNotBeEmpty()
        }
    }

    test("timeout after delay") {
        runTest {
            val service = SlowService()

            shouldThrow<TimeoutCancellationException> {
                withTimeout(100) {
                    service.slowOperation() // Takes > 100ms
                }
            }
        }
    }
})
```

#### Testing Flows

#### Testing Flows
### 测试 Flows

```kotlin
import io.kotest.matchers.collections.shouldContainInOrder
import kotlinx.coroutines.flow.MutableSharedFlow
import kotlinx.coroutines.flow.toList
import kotlinx.coroutines.launch
import kotlinx.coroutines.test.advanceTimeBy
import kotlinx.coroutines.test.runTest

class FlowServiceTest : FunSpec({
    test("observeUsers emits updates") {
        runTest {
            val service = UserFlowService()

            val emissions = service.observeUsers()
                .take(3)
                .toList()

            emissions shouldHaveSize 3
            emissions.last().shouldNotBeEmpty()
        }
    }

    test("searchUsers debounces input") {
        runTest {
            val service = SearchService()
            val queries = MutableSharedFlow<String>()

            val results = mutableListOf<List<User>>()
            val job = launch {
                service.searchUsers(queries).collect { results.add(it) }
            }

            queries.emit("a")
            queries.emit("ab")
            queries.emit("abc") // Only this should trigger search
            advanceTimeBy(500)

            results shouldHaveSize 1
            job.cancel()
        }
    }
})
```

#### TestDispatcher

#### TestDispatcher
#### TestDispatcher

```kotlin
import kotlinx.coroutines.test.StandardTestDispatcher
import kotlinx.coroutines.test.advanceUntilIdle

class DispatcherTest : FunSpec({
    test("uses test dispatcher for controlled execution") {
        val dispatcher = StandardTestDispatcher()

        runTest(dispatcher) {
            var completed = false

            launch {
                delay(1000)
                completed = true
            }

            completed shouldBe false
            advanceTimeBy(1000)
            completed shouldBe true
        }
    }
})
```

### Property-Based Testing

### Property-Based Testing
### 基于属性的测试

#### Kotest Property Testing

#### Kotest Property Testing
#### Kotest 属性测试

```kotlin
import io.kotest.core.spec.style.FunSpec
import io.kotest.property.Arb
import io.kotest.property.arbitrary.*
import io.kotest.property.forAll
import io.kotest.property.checkAll
import kotlinx.serialization.json.Json
import kotlinx.serialization.encodeToString
import kotlinx.serialization.decodeFromString

// Note: The serialization roundtrip test below requires the User data class
// to be annotated with @Serializable (from kotlinx.serialization).
// 注意：下面的序列化往返测试要求 User 数据类使用 @Serializable 注解（来自 kotlinx.serialization）。

class PropertyTest : FunSpec({
    test("string reverse is involutory") {
        forAll<String> { s ->
            s.reversed().reversed() == s
        }
    }

    test("list sort is idempotent") {
        forAll(Arb.list(Arb.int())) { list ->
            list.sorted() == list.sorted().sorted()
        }
    }

    test("serialization roundtrip preserves data") {
        checkAll(Arb.bind(Arb.string(1..50), Arb.string(5..100)) { name, email ->
            User(name = name, email = "$email@test.com")
        }) { user ->
            val json = Json.encodeToString(user)
            val decoded = Json.decodeFromString<User>(json)
            decoded shouldBe user
        }
    }
})
```

#### Custom Generators

#### Custom Generators
#### 自定义生成器

```kotlin
val userArb: Arb<User> = Arb.bind(
    Arb.string(minSize = 1, maxSize = 50),
    Arb.email(),
    Arb.enum<Role>(),
) { name, email, role ->
    User(
        id = UserId(UUID.randomUUID().toString()),
        name = name,
        email = Email(email),
        role = role,
    )
}

val moneyArb: Arb<Money> = Arb.bind(
    Arb.long(1L..1_000_000L),
    Arb.enum<Currency>(),
) { amount, currency ->
    Money(amount, currency)
}
```

### Data-Driven Testing

### Data-Driven Testing
### 数据驱动测试

#### withData in Kotest

#### withData in Kotest
#### Kotest 中的 withData

```kotlin
class ParserTest : FunSpec({
    context("parsing valid dates") {
        withData(
            "2026-01-15" to LocalDate(2026, 1, 15),
            "2026-12-31" to LocalDate(2026, 12, 31),
            "2000-01-01" to LocalDate(2000, 1, 1),
        ) { (input, expected) ->
            parseDate(input) shouldBe expected
        }
    }

    context("rejecting invalid dates") {
        withData(
            nameFn = { "rejects '$it'" },
            "not-a-date",
            "2026-13-01",
            "2026-00-15",
            "",
        ) { input ->
            shouldThrow<DateParseException> {
                parseDate(input)
            }
        }
    }
})
```

### Test Lifecycle and Fixtures

### Test Lifecycle and Fixtures
### 测试生命周期和 Fixtures

#### BeforeTest / AfterTest

#### BeforeTest / AfterTest
#### BeforeTest / AfterTest

```kotlin
class DatabaseTest : FunSpec({
    lateinit var db: Database

    beforeSpec {
        db = Database.connect("jdbc:h2:mem:test;DB_CLOSE_DELAY=-1")
        transaction(db) {
            SchemaUtils.create(UsersTable)
        }
    }

    afterSpec {
        transaction(db) {
            SchemaUtils.drop(UsersTable)
        }
    }

    beforeTest {
        transaction(db) {
            UsersTable.deleteAll()
        }
    }

    test("insert and retrieve user") {
        transaction(db) {
            UsersTable.insert {
                it[name] = "Alice"
                it[email] = "alice@example.com"
            }
        }

        val users = transaction(db) {
            UsersTable.selectAll().map { it[UsersTable.name] }
        }

        users shouldContain "Alice"
    }
})
```

#### Kotest Extensions

#### Kotest Extensions
#### Kotest 扩展

```kotlin
// Reusable test extension
// 可重用的测试扩展
class DatabaseExtension : BeforeSpecListener, AfterSpecListener {
    lateinit var db: Database

    override suspend fun beforeSpec(spec: Spec) {
        db = Database.connect("jdbc:h2:mem:test;DB_CLOSE_DELAY=-1")
    }

    override suspend fun afterSpec(spec: Spec) {
        // cleanup
        // 清理
    }
}

class UserRepositoryTest : FunSpec({
    val dbExt = DatabaseExtension()
    register(dbExt)

    test("save and find user") {
        val repo = UserRepository(dbExt.db)
        // ...
    }
})
```

### Kover Coverage

### Kover Coverage
### Kover 覆盖率

#### Gradle Configuration

#### Gradle Configuration
#### Gradle 配置

```kotlin
// build.gradle.kts
plugins {
    id("org.jetbrains.kotlinx.kover") version "0.9.7"
}

kover {
    reports {
        total {
            html { onCheck = true }
            xml { onCheck = true }
        }
        filters {
            excludes {
                classes("*.generated.*", "*.config.*")
            }
        }
        verify {
            rule {
                minBound(80) // Fail build below 80% coverage
                // 覆盖率低于 80% 时构建失败
            }
        }
    }
}
```

#### Coverage Commands

#### Coverage Commands
#### 覆盖率命令

```bash
# Run tests with coverage
# 运行带覆盖率的测试
./gradlew koverHtmlReport

# Verify coverage thresholds
# 验证覆盖率阈值
./gradlew koverVerify

# XML report for CI
# CI 用的 XML 报告
./gradlew koverXmlReport

# View HTML report (use the command for your OS)
# 查看 HTML 报告（根据你的操作系统使用对应命令）
# macOS:   open build/reports/kover/html/index.html
# Linux:   xdg-open build/reports/kover/html/index.html
# Windows: start build/reports/kover/html/index.html
```

#### Coverage Targets

#### Coverage Targets
#### 覆盖率目标

| Code Type | Target | Target zh-CN |
|-----------|--------|-------------|
| Critical business logic | 100% | 关键业务逻辑 | 100% |
| Public APIs | 90%+ | 公共 API | 90%+ |
| General code | 80%+ | 一般代码 | 80%+ |
| Generated / config code | Exclude | 生成/配置代码 | 排除 |

### Ktor testApplication Testing

### Ktor testApplication Testing
### Ktor testApplication 测试

```kotlin
class ApiRoutesTest : FunSpec({
    test("GET /users returns list") {
        testApplication {
            application {
                configureRouting()
                configureSerialization()
            }

            val response = client.get("/users")

            response.status shouldBe HttpStatusCode.OK
            val users = response.body<List<UserResponse>>()
            users.shouldNotBeEmpty()
        }
    }

    test("POST /users creates user") {
        testApplication {
            application {
                configureRouting()
                configureSerialization()
            }

            val response = client.post("/users") {
                contentType(ContentType.Application.Json)
                setBody(CreateUserRequest("Alice", "alice@example.com"))
            }

            response.status shouldBe HttpStatusCode.Created
        }
    }
})
```

### Testing Commands

### Testing Commands
### 测试命令

```bash
# Run all tests
# 运行所有测试
./gradlew test

# Run specific test class
# 运行特定测试类
./gradlew test --tests "com.example.UserServiceTest"

# Run specific test
# 运行特定测试
./gradlew test --tests "com.example.UserServiceTest.getUser returns user when found"

# Run with verbose output
# 使用详细输出运行
./gradlew test --info

# Run with coverage
# 运行并生成覆盖率报告
./gradlew koverHtmlReport

# Run detekt (static analysis)
# 运行 detekt（静态分析）
./gradlew detekt

# Run ktlint (formatting check)
# 运行 ktlint（格式检查）
./gradlew ktlintCheck

# Continuous testing
# 持续测试
./gradlew test --continuous
```

### Best Practices

### Best Practices
### 最佳实践

**DO:**
**做：**
- Write tests FIRST (TDD)
  - 先写测试（TDD）
- Use Kotest's spec styles consistently across the project
  - 在整个项目中一致地使用 Kotest 的 spec 风格
- Use MockK's `coEvery`/`coVerify` for suspend functions
  - 对挂起函数使用 MockK 的 `coEvery`/`coVerify`
- Use `runTest` for coroutine testing
  - 对协程测试使用 `runTest`
- Test behavior, not implementation
  - 测试行为，而非实现
- Use property-based testing for pure functions
  - 对纯函数使用基于属性的测试
- Use `data class` test fixtures for clarity
  - 使用 `data class` 测试 fixtures 以提高清晰度

**DON'T:**
**不要：**
- Mix testing frameworks (pick Kotest and stick with it)
  - 混用测试框架（选择 Kotest 并坚持使用）
- Mock data classes (use real instances)
  - 模拟 data class（使用真实实例）
- Use `Thread.sleep()` in coroutine tests (use `advanceTimeBy`)
  - 在协程测试中使用 `Thread.sleep()`（使用 `advanceTimeBy`）
- Skip the RED phase in TDD
  - 在 TDD 中跳过红色阶段
- Test private functions directly
  - 直接测试私有函数
- Ignore flaky tests
  - 忽略不稳定的测试

### Integration with CI/CD

### Integration with CI/CD
### CI/CD 集成

```yaml
# GitHub Actions example
# GitHub Actions 示例
test:
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v4
    - uses: actions/setup-java@v4
      with:
        distribution: 'temurin'
        java-version: '21'

    - name: Run tests with coverage
      run: ./gradlew test koverXmlReport

    - name: Verify coverage
      run: ./gradlew koverVerify

    - name: Upload coverage
      uses: codecov/codecov-action@v5
      with:
        files: build/reports/kover/report.xml
        token: ${{ secrets.CODECOV_TOKEN }}
```

**Remember**: Tests are documentation. They show how your Kotlin code is meant to be used. Use Kotest's expressive matchers to make tests readable and MockK for clean mocking of dependencies.
**记住**：测试即文档。它们展示了你的 Kotlin 代码应该如何使用。使用 Kotest 的表达性匹配器使测试可读，并使用 MockK 进行干净的依赖模拟。
