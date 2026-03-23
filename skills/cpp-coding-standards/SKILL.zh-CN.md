---
name: cpp-coding-standards
description: C++ coding standards based on the C++ Core Guidelines (isocpp.github.io). Use when writing, reviewing, or refactoring C++ code to enforce modern, safe, and idiomatic practices.
description zh-CN: 基于 C++ Core Guidelines (isocpp.github.io) 的 C++ 编码标准。在编写、审查或重构 C++ 代码时使用，以强制执行现代、安全和惯用的实践。
origin: ECC
---

# C++ Coding Standards (C++ Core Guidelines)
# C++ Coding Standards (C++ Core Guidelines)
# C++ 编码标准（C++ 核心指南）

Comprehensive coding standards for modern C++ (C++17/20/23) derived from the [C++ Core Guidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines). Enforces type safety, resource safety, immutability, and clarity.
## Comprehensive coding standards for modern C++ (C++17/20/23) derived from the [C++ Core Guidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines). Enforces type safety, resource safety, immutability, and clarity.
## 源自 [C++ Core Guidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines) 的现代 C++（C++17/20/23）综合编码标准。强制类型安全、资源安全、不可变性和清晰性。

## When to Use
## When to Use
## 何时使用

- Writing new C++ code (classes, functions, templates)
  - 编写新的 C++ 代码（类、函数、模板）
- Reviewing or refactoring existing C++ code
  - 审查或重构现有 C++ 代码
- Making architectural decisions in C++ projects
  - 在 C++ 项目中做出架构决策
- Enforcing consistent style across a C++ codebase
  - 在 C++ 代码库中强制执行一致的风格
- Choosing between language features (e.g., `enum` vs `enum class`, raw pointer vs smart pointer)
  - 在语言特性之间选择（例如 `enum` vs `enum class`、裸指针 vs 智能指针）

### When NOT to Use
### When NOT to Use
### 何时不使用

- Non-C++ projects
  - 非 C++ 项目
- Legacy C codebases that cannot adopt modern C++ features
  - 无法采用现代 C++ 特性的遗留 C 代码库
- Embedded/bare-metal contexts where specific guidelines conflict with hardware constraints (adapt selectively)
  - 特定指南与硬件约束冲突的嵌入式/裸机上下文（选择性适应）

## Cross-Cutting Principles
## Cross-Cutting Principles
## 横切原则

These themes recur across the entire guidelines and form the foundation:
## These themes recur across the entire guidelines and form the foundation.
## 这些主题贯穿整个指南并构成基础：

1. **RAII everywhere** (P.8, R.1, E.6, CP.20): Bind resource lifetime to object lifetime
   - **到处使用 RAII**（P.8、R.1、E.6、CP.20）：将资源生命周期绑定到对象生命周期
2. **Immutability by default** (P.10, Con.1-5, ES.25): Start with `const`/`constexpr`; mutability is the exception
   - **默认不可变**（P.10、Con.1-5、ES.25）：从 `const`/`constexpr` 开始；可变是例外
3. **Type safety** (P.4, I.4, ES.46-49, Enum.3): Use the type system to prevent errors at compile time
   - **类型安全**（P.4、I.4、ES.46-49、Enum.3）：使用类型系统在编译时防止错误
4. **Express intent** (P.3, F.1, NL.1-2, T.10): Names, types, and concepts should communicate purpose
   - **表达意图**（P.3、F.1、NL.1-2、T.10）：名称、类型和概念应传达目的
5. **Minimize complexity** (F.2-3, ES.5, Per.4-5): Simple code is correct code
   - **最小化复杂性**（F.2-3、ES.5、Per.4-5）：简单的代码是正确的代码
6. **Value semantics over pointer semantics** (C.10, R.3-5, F.20, CP.31): Prefer returning by value and scoped objects
   - **值语义优于指针语义**（C.10、R.3-5、F.20、CP.31）：优先通过值返回和作用域对象

## Philosophy & Interfaces (P.*, I.*)
## Philosophy & Interfaces (P.*, I.*)
## 哲学与接口（P.*、I.*）

### Key Rules
### Key Rules
### 关键规则

| Rule | Summary |
|------|---------|
| **P.1** | Express ideas directly in code |
| **P.1** | 直接在代码中表达想法 |
| **P.3** | Express intent |
| **P.3** | 表达意图 |
| **P.4** | Ideally, a program should be statically type safe |
| **P.4** | 理想情况下，程序应该是静态类型安全的 |
| **P.5** | Prefer compile-time checking to run-time checking |
| **P.5** | 优先编译时检查而非运行时检查 |
| **P.8** | Don't leak any resources |
| **P.8** | 不要泄漏任何资源 |
| **P.10** | Prefer immutable data to mutable data |
| **P.10** | 优先不可变数据而非可变数据 |
| **I.1** | Make interfaces explicit |
| **I.1** | 使接口明确 |
| **I.2** | Avoid non-const global variables |
| **I.2** | 避免非 const 全局变量 |
| **I.4** | Make interfaces precisely and strongly typed |
| **I.4** | 使接口精确且强类型 |
| **I.11** | Never transfer ownership by a raw pointer or reference |
| **I.11** | 绝不通过裸指针或引用传递所有权 |
| **I.23** | Keep the number of function arguments low |
| **I.23** | 保持函数参数数量低 |

### DO
### DO
### 正确示例

```cpp
// P.10 + I.4: Immutable, strongly typed interface
// P.10 + I.4: 不可变、强类型接口
struct Temperature {
    double kelvin;
};

Temperature boil(const Temperature& water);
```

### DON'T
### DON'T
### 错误示例

```cpp
// Weak interface: unclear ownership, unclear units
// 弱接口：所有权不清，单位不清
double boil(double* temp);

// Non-const global variable
// 非 const 全局变量
int g_counter = 0;  // I.2 violation
```

## Functions (F.*)
## Functions (F.*)
## 函数（F.*）

### Key Rules
### Key Rules
### 关键规则

| Rule | Summary |
|------|---------|
| **F.1** | Package meaningful operations as carefully named functions |
| **F.1** | 将有意义的操作打包为精心命名的函数 |
| **F.2** | A function should perform a single logical operation |
| **F.2** | 函数应执行单一逻辑操作 |
| **F.3** | Keep functions short and simple |
| **F.3** | 保持函数简短和简单 |
| **F.4** | If a function might be evaluated at compile time, declare it `constexpr` |
| **F.4** | 如果函数可能在编译时求值，声明为 `constexpr` |
| **F.6** | If your function must not throw, declare it `noexcept` |
| **F.6** | 如果函数不能抛出异常，声明为 `noexcept` |
| **F.8** | Prefer pure functions |
| **F.8** | 优先纯函数 |
| **F.16** | For "in" parameters, pass cheaply-copied types by value and others by `const&` |
| **F.16** | 对于"输入"参数，通过值传递廉价复制的类型，其他通过 `const&` |
| **F.20** | For "out" values, prefer return values to output parameters |
| **F.20** | 对于"输出"值，优先返回值而非输出参数 |
| **F.21** | To return multiple "out" values, prefer returning a struct |
| **F.21** | 要返回多个"输出"值，优先返回结构体 |
| **F.43** | Never return a pointer or reference to a local object |
| **F.43** | 绝不返回指向局部对象的指针或引用 |

### Parameter Passing
### Parameter Passing
### 参数传递

```cpp
// F.16: Cheap types by value, others by const&
// F.16: 廉价类型通过值传递，其他通过 const&
void print(int x);                           // cheap: by value
void analyze(const std::string& data);       // expensive: by const&
void transform(std::string s);               // sink: by value (will move)

// F.20 + F.21: Return values, not output parameters
// F.20 + F.21: 返回值而非输出参数
struct ParseResult {
    std::string token;
    int position;
};

ParseResult parse(std::string_view input);   // GOOD: return struct

// BAD: output parameters
// 错误：输出参数
void parse(std::string_view input,
           std::string& token, int& pos);    // avoid this
```

### Pure Functions and constexpr
### Pure Functions and constexpr
### 纯函数和 constexpr

```cpp
// F.4 + F.8: Pure, constexpr where possible
// F.4 + F.8: 尽可能使用纯函数和 constexpr
constexpr int factorial(int n) noexcept {
    return (n <= 1) ? 1 : n * factorial(n - 1);
}

static_assert(factorial(5) == 120);
```

### Anti-Patterns
### Anti-Patterns
### 反模式

- Returning `T&&` from functions (F.45)
  - 从函数返回 `T&&`（F.45）
- Using `va_arg` / C-style variadics (F.55)
  - 使用 `va_arg`/C 风格可变参数（F.55）
- Capturing by reference in lambdas passed to other threads (F.53)
  - 在传递给其他线程的 lambda 中按引用捕获（F.53）
- Returning `const T` which inhibits move semantics (F.49)
  - 返回 `const T` 抑制移动语义（F.49）

## Classes & Class Hierarchies (C.*)
## Classes & Class Hierarchies (C.*)
## 类和类层次结构（C.*）

### Key Rules
### Key Rules
### 关键规则

| Rule | Summary |
|------|---------|
| **C.2** | Use `class` if invariant exists; `struct` if data members vary independently |
| **C.2** | 如果存在不变量使用 `class`；如果数据成员独立变化使用 `struct` |
| **C.9** | Minimize exposure of members |
| **C.9** | 最小化成员暴露 |
| **C.20** | If you can avoid defining default operations, do (Rule of Zero) |
| **C.20** | 如果可以避免定义默认操作，就这样（零规则） |
| **C.21** | If you define or `=delete` any copy/move/destructor, handle them all (Rule of Five) |
| **C.21** | 如果你定义或 `=delete` 任何复制/移动/析构函数，处理全部（五规则） |
| **C.35** | Base class destructor: public virtual or protected non-virtual |
| **C.35** | 基类析构函数：public virtual 或 protected non-virtual |
| **C.41** | A constructor should create a fully initialized object |
| **C.41** | 构造函数应创建完全初始化的对象 |
| **C.46** | Declare single-argument constructors `explicit` |
| **C.46** | 将单参数构造函数声明为 `explicit` |
| **C.67** | A polymorphic class should suppress public copy/move |
| **C.67** | 多态类应禁止公共复制/移动 |
| **C.128** | Virtual functions: specify exactly one of `virtual`, `override`, or `final` |
| **C.128** | 虚函数：准确指定 `virtual`、`override` 或 `final` 之一 |

### Rule of Zero
### Rule of Zero
### 零规则

```cpp
// C.20: Let the compiler generate special members
// C.20: 让编译器生成特殊成员
struct Employee {
    std::string name;
    std::string department;
    int id;
    // No destructor, copy/move constructors, or assignment operators needed
    // 不需要析构函数、复制/移动构造函数或赋值运算符
};
```

### Rule of Five
### Rule of Five
### 五规则

```cpp
// C.21: If you must manage a resource, define all five
// C.21: 如果必须管理资源，定义全部五个
class Buffer {
public:
    explicit Buffer(std::size_t size)
        : data_(std::make_unique<char[]>(size)), size_(size) {}

    ~Buffer() = default;

    Buffer(const Buffer& other)
        : data_(std::make_unique<char[]>(other.size_)), size_(other.size_) {
        std::copy_n(other.data_.get(), size_, data_.get());
    }

    Buffer& operator=(const Buffer& other) {
        if (this != &other) {
            auto new_data = std::make_unique<char[]>(other.size_);
            std::copy_n(other.data_.get(), other.size_, new_data.get());
            data_ = std::move(new_data);
            size_ = other.size_;
        }
        return *this;
    }

    Buffer(Buffer&&) noexcept = default;
    Buffer& operator=(Buffer&&) noexcept = default;

private:
    std::unique_ptr<char[]> data_;
    std::size_t size_;
};
```

### Class Hierarchy
### Class Hierarchy
### 类层次结构

```cpp
// C.35 + C.128: Virtual destructor, use override
// C.35 + C.128: 虚析构函数，使用 override
class Shape {
public:
    virtual ~Shape() = default;
    virtual double area() const = 0;  // C.121: pure interface
};

class Circle : public Shape {
public:
    explicit Circle(double r) : radius_(r) {}
    double area() const override { return 3.14159 * radius_ * radius_; }

private:
    double radius_;
};
```

### Anti-Patterns
### Anti-Patterns
### 反模式

- Calling virtual functions in constructors/destructors (C.82)
  - 在构造函数/析构函数中调用虚函数（C.82）
- Using `memset`/`memcpy` on non-trivial types (C.90)
  - 对非平凡类型使用 `memset`/`memcpy`（C.90）
- Providing different default arguments for virtual function and overrider (C.140)
  - 为虚函数和覆盖函数提供不同的默认参数（C.140）
- Making data members `const` or references, which suppresses move/copy (C.12)
  - 使数据成员 `const` 或引用，这会抑制移动/复制（C.12）

## Resource Management (R.*)
## Resource Management (R.*)
## 资源管理（R.*）

### Key Rules
### Key Rules
### 关键规则

| Rule | Summary |
|------|---------|
| **R.1** | Manage resources automatically using RAII |
| **R.1** | 使用 RAII 自动管理资源 |
| **R.3** | A raw pointer (`T*`) is non-owning |
| **R.3** | 裸指针（`T*`）是非拥有的 |
| **R.5** | Prefer scoped objects; don't heap-allocate unnecessarily |
| **R.5** | 优先作用域对象；不要不必要地在堆上分配 |
| **R.10** | Avoid `malloc()`/`free()` |
| **R.10** | 避免 `malloc()`/`free()` |
| **R.11** | Avoid calling `new` and `delete` explicitly |
| **R.11** | 避免显式调用 `new` 和 `delete` |
| **R.20** | Use `unique_ptr` or `shared_ptr` to represent ownership |
| **R.20** | 使用 `unique_ptr` 或 `shared_ptr` 表示所有权 |
| **R.21** | Prefer `unique_ptr` over `shared_ptr` unless sharing ownership |
| **R.21** | 除非共享所有权，否则优先使用 `unique_ptr` 而非 `shared_ptr` |
| **R.22** | Use `make_shared()` to make `shared_ptr`s |
| **R.22** | 使用 `make_shared()` 创建 `shared_ptr` |

### Smart Pointer Usage
### Smart Pointer Usage
### 智能指针使用

```cpp
// R.11 + R.20 + R.21: RAII with smart pointers
// R.11 + R.20 + R.21: 使用智能指针的 RAII
auto widget = std::make_unique<Widget>("config");  // unique ownership
auto cache  = std::make_shared<Cache>(1024);        // shared ownership

// R.3: Raw pointer = non-owning observer
// R.3: 裸指针 = 非拥有观察者
void render(const Widget* w) {  // does NOT own w
    if (w) w->draw();
}

render(widget.get());
```

### RAII Pattern
### RAII Pattern
### RAII 模式

```cpp
// R.1: Resource acquisition is initialization
// R.1: 资源获取即初始化
class FileHandle {
public:
    explicit FileHandle(const std::string& path)
        : handle_(std::fopen(path.c_str(), "r")) {
        if (!handle_) throw std::runtime_error("Failed to open: " + path);
    }

    ~FileHandle() {
        if (handle_) std::fclose(handle_);
    }

    FileHandle(const FileHandle&) = delete;
    FileHandle& operator=(const FileHandle&) = delete;
    FileHandle(FileHandle&& other) noexcept
        : handle_(std::exchange(other.handle_, nullptr)) {}
    FileHandle& operator=(FileHandle&& other) noexcept {
        if (this != &other) {
            if (handle_) std::fclose(handle_);
            handle_ = std::exchange(other.handle_, nullptr);
        }
        return *this;
    }

private:
    std::FILE* handle_;
};
```

### Anti-Patterns
### Anti-Patterns
### 反模式

- Naked `new`/`delete` (R.11)
  - 裸 `new`/`delete`（R.11）
- `malloc()`/`free()` in C++ code (R.10)
  - 在 C++ 代码中使用 `malloc()`/`free()`（R.10）
- Multiple resource allocations in a single expression (R.13 -- exception safety hazard)
  - 在单个表达式中分配多个资源（R.13——异常安全危险）
- `shared_ptr` where `unique_ptr` suffices (R.21)
  - 使用 `shared_ptr` 而 `unique_ptr` 足够时（R.21）

## Expressions & Statements (ES.*)
## Expressions & Statements (ES.*)
## 表达式和语句（ES.*）

### Key Rules
### Key Rules
### 关键规则

| Rule | Summary |
|------|---------|
| **ES.5** | Keep scopes small |
| **ES.5** | 保持作用域小 |
| **ES.20** | Always initialize an object |
| **ES.20** | 始终初始化对象 |
| **ES.23** | Prefer `{}` initializer syntax |
| **ES.23** | 优先使用 `{}` 初始化语法 |
| **ES.25** | Declare objects `const` or `constexpr` unless modification is intended |
| **ES.25** | 除非打算修改，否则声明对象为 `const` 或 `constexpr` |
| **ES.28** | Use lambdas for complex initialization of `const` variables |
| **ES.28** | 使用 lambda 进行 `const` 变量的复杂初始化 |
| **ES.45** | Avoid magic constants; use symbolic constants |
| **ES.45** | 避免幻数；使用符号常量 |
| **ES.46** | Avoid narrowing/lossy arithmetic conversions |
| **ES.46** | 避免窄化/有损算术转换 |
| **ES.47** | Use `nullptr` rather than `0` or `NULL` |
| **ES.47** | 使用 `nullptr` 而非 `0` 或 `NULL` |
| **ES.48** | Avoid casts |
| **ES.48** | 避免类型转换 |
| **ES.50** | Don't cast away `const` |
| **ES.50** | 不要去除 `const` |

### Initialization
### Initialization
### 初始化

```cpp
// ES.20 + ES.23 + ES.25: Always initialize, prefer {}, default to const
// ES.20 + ES.23 + ES.25: 始终初始化，优先使用 {}，默认 const
const int max_retries{3};
const std::string name{"widget"};
const std::vector<int> primes{2, 3, 5, 7, 11};

// ES.28: Lambda for complex const initialization
// ES.28: 使用 lambda 进行复杂的 const 初始化
const auto config = [&] {
    Config c;
    c.timeout = std::chrono::seconds{30};
    c.retries = max_retries;
    c.verbose = debug_mode;
    return c;
}();
```

### Anti-Patterns
### Anti-Patterns
### 反模式

- Uninitialized variables (ES.20)
  - 未初始化变量（ES.20）
- Using `0` or `NULL` as pointer (ES.47 -- use `nullptr`)
  - 使用 `0` 或 `NULL` 作为指针（ES.47——使用 `nullptr`）
- C-style casts (ES.48 -- use `static_cast`, `const_cast`, etc.)
  - C 风格转换（ES.48——使用 `static_cast`、`const_cast` 等）
- Casting away `const` (ES.50)
  - 去除 `const`（ES.50）
- Magic numbers without named constants (ES.45)
  - 没有命名常量的幻数（ES.45）
- Mixing signed and unsigned arithmetic (ES.100)
  - 混合有符号和无符号算术（ES.100）
- Reusing names in nested scopes (ES.12)
  - 在嵌套作用域中重用名称（ES.12）

## Error Handling (E.*)
## Error Handling (E.*)
## 错误处理（E.*）

### Key Rules
### Key Rules
### 关键规则

| Rule | Summary |
|------|---------|
| **E.1** | Develop an error-handling strategy early in a design |
| **E.1** | 在设计早期制定错误处理策略 |
| **E.2** | Throw an exception to signal that a function can't perform its assigned task |
| **E.2** | 抛出异常以表示函数无法执行其分配的任务 |
| **E.6** | Use RAII to prevent leaks |
| **E.6** | 使用 RAII 防止泄漏 |
| **E.12** | Use `noexcept` when throwing is impossible or unacceptable |
| **E.12** | 当抛出不可能或不可接受时使用 `noexcept` |
| **E.14** | Use purpose-designed user-defined types as exceptions |
| **E.14** | 使用专门设计的用户定义类型作为异常 |
| **E.15** | Throw by value, catch by reference |
| **E.15** | 通过值抛出，通过引用捕获 |
| **E.16** | Destructors, deallocation, and swap must never fail |
| **E.16** | 析构函数、释放和交换绝不能失败 |
| **E.17** | Don't try to catch every exception in every function |
| **E.17** | 不要试图在每个函数中捕获每个异常 |

### Exception Hierarchy
### Exception Hierarchy
### 异常层次结构

```cpp
// E.14 + E.15: Custom exception types, throw by value, catch by reference
// E.14 + E.15: 自定义异常类型，通过值抛出，通过引用捕获
class AppError : public std::runtime_error {
public:
    using std::runtime_error::runtime_error;
};

class NetworkError : public AppError {
public:
    NetworkError(const std::string& msg, int code)
        : AppError(msg), status_code(code) {}
    int status_code;
};

void fetch_data(const std::string& url) {
    // E.2: Throw to signal failure
    // E.2: 抛出以表示失败
    throw NetworkError("connection refused", 503);
}

void run() {
    try {
        fetch_data("https://api.example.com");
    } catch (const NetworkError& e) {
        log_error(e.what(), e.status_code);
    } catch (const AppError& e) {
        log_error(e.what());
    }
    // E.17: Don't catch everything here -- let unexpected errors propagate
    // E.17: 不要在这里捕获所有——让意外错误传播
}
```

### Anti-Patterns
### Anti-Patterns
### 反模式

- Throwing built-in types like `int` or string literals (E.14)
  - 抛出内置类型如 `int` 或字符串字面量（E.14）
- Catching by value (slicing risk) (E.15)
  - 通过值捕获（切片风险）（E.15）
- Empty catch blocks that silently swallow errors
  - 静默吞咽错误的空 catch 块
- Using exceptions for flow control (E.3)
  - 使用异常进行流程控制（E.3）
- Error handling based on global state like `errno` (E.28)
  - 基于全局状态如 `errno` 的错误处理（E.28）

## Constants & Immutability (Con.*)
## Constants & Immutability (Con.*)
## 常量和不可变性（Con.*）

### All Rules
### All Rules
### 所有规则

| Rule | Summary |
|------|---------|
| **Con.1** | By default, make objects immutable |
| **Con.1** | 默认情况下，使对象不可变 |
| **Con.2** | By default, make member functions `const` |
| **Con.2** | 默认情况下，使成员函数 `const` |
| **Con.3** | By default, pass pointers and references to `const` |
| **Con.3** | 默认情况下，传递指针和引用给 `const` |
| **Con.4** | Use `const` for values that don't change after construction |
| **Con.4** | 对构造后不更改的值使用 `const` |
| **Con.5** | Use `constexpr` for values computable at compile time |
| **Con.5** | 对可在编译时计算的值使用 `constexpr` |

```cpp
// Con.1 through Con.5: Immutability by default
// Con.1 到 Con.5: 默认不可变
class Sensor {
public:
    explicit Sensor(std::string id) : id_(std::move(id)) {}

    // Con.2: const member functions by default
    // Con.2: 默认 const 成员函数
    const std::string& id() const { return id_; }
    double last_reading() const { return reading_; }

    // Only non-const when mutation is required
    // 仅在需要变更时使用非 const
    void record(double value) { reading_ = value; }

private:
    const std::string id_;  // Con.4: never changes after construction
    double reading_{0.0};
};

// Con.3: Pass by const reference
// Con.3: 通过 const 引用传递
void display(const Sensor& s) {
    std::cout << s.id() << ": " << s.last_reading() << '\n';
}

// Con.5: Compile-time constants
// Con.5: 编译时常量
constexpr double PI = 3.14159265358979;
constexpr int MAX_SENSORS = 256;
```

## Concurrency & Parallelism (CP.*)
## Concurrency & Parallelism (CP.*)
## 并发与并行（CP.*）

### Key Rules
### Key Rules
### 关键规则

| Rule | Summary |
|------|---------|
| **CP.2** | Avoid data races |
| **CP.2** | 避免数据竞争 |
| **CP.3** | Minimize explicit sharing of writable data |
| **CP.3** | 最小化可变数据的显式共享 |
| **CP.4** | Think in terms of tasks, rather than threads |
| **CP.4** | 从任务角度思考，而非线程 |
| **CP.8** | Don't use `volatile` for synchronization |
| **CP.8** | 不要使用 `volatile` 进行同步 |
| **CP.20** | Use RAII, never plain `lock()`/`unlock()` |
| **CP.20** | 使用 RAII，绝不要使用裸 `lock()`/`unlock()` |
| **CP.21** | Use `std::scoped_lock` to acquire multiple mutexes |
| **CP.21** | 使用 `std::scoped_lock` 获取多个互斥锁 |
| **CP.22** | Never call unknown code while holding a lock |
| **CP.22** | 持有锁时绝不调用未知代码 |
| **CP.42** | Don't wait without a condition |
| **CP.42** | 不要在没有条件的情况下等待 |
| **CP.44** | Remember to name your `lock_guard`s and `unique_lock`s |
| **CP.44** | 记住命名你的 `lock_guard` 和 `unique_lock` |
| **CP.100** | Don't use lock-free programming unless you absolutely have to |
| **CP.100** | 除非绝对必要，否则不要使用无锁编程 |

### Safe Locking
### Safe Locking
### 安全锁定

```cpp
// CP.20 + CP.44: RAII locks, always named
// CP.20 + CP.44: RAII 锁，总是命名
class ThreadSafeQueue {
public:
    void push(int value) {
        std::lock_guard<std::mutex> lock(mutex_);  // CP.44: named!
        queue_.push(value);
        cv_.notify_one();
    }

    int pop() {
        std::unique_lock<std::mutex> lock(mutex_);
        // CP.42: Always wait with a condition
        // CP.42: 总是使用条件等待
        cv_.wait(lock, [this] { return !queue_.empty(); });
        const int value = queue_.front();
        queue_.pop();
        return value;
    }

private:
    std::mutex mutex_;             // CP.50: mutex with its data
    std::condition_variable cv_;
    std::queue<int> queue_;
};
```

### Multiple Mutexes
### Multiple Mutexes
### 多个互斥锁

```cpp
// CP.21: std::scoped_lock for multiple mutexes (deadlock-free)
// CP.21: std::scoped_lock 用于多个互斥锁（无死锁）
void transfer(Account& from, Account& to, double amount) {
    std::scoped_lock lock(from.mutex_, to.mutex_);
    from.balance_ -= amount;
    to.balance_ += amount;
}
```

### Anti-Patterns
### Anti-Patterns
### 反模式

- `volatile` for synchronization (CP.8 -- it's for hardware I/O only)
  - 使用 `volatile` 进行同步（CP.8——它仅用于硬件 I/O）
- Detaching threads (CP.26 -- lifetime management becomes nearly impossible)
  - 分离线程（CP.26——生命周期管理几乎不可能）
- Unnamed lock guards: `std::lock_guard<std::mutex>(m);` destroys immediately (CP.44)
  - 未命名的锁守卫：`std::lock_guard<std::mutex>(m);` 立即销毁（CP.44）
- Holding locks while calling callbacks (CP.22 -- deadlock risk)
  - 在调用回调时持有锁（CP.22——死锁风险）
- Lock-free programming without deep expertise (CP.100)
  - 没有深度专业知识的无锁编程（CP.100）

## Templates & Generic Programming (T.*)
## Templates & Generic Programming (T.*)
## 模板和泛型编程（T.*）

### Key Rules
### Key Rules
### 关键规则

| Rule | Summary |
|------|---------|
| **T.1** | Use templates to raise the level of abstraction |
| **T.1** | 使用模板提高抽象级别 |
| **T.2** | Use templates to express algorithms for many argument types |
| **T.2** | 使用模板表达多参数类型的算法 |
| **T.10** | Specify concepts for all template arguments |
| **T.10** | 为所有模板参数指定概念 |
| **T.11** | Use standard concepts whenever possible |
| **T.11** | 尽可能使用标准概念 |
| **T.13** | Prefer shorthand notation for simple concepts |
| **T.13** | 对简单概念优先使用简写表示法 |
| **T.43** | Prefer `using` over `typedef` |
| **T.43** | 优先使用 `using` 而非 `typedef` |
| **T.120** | Use template metaprogramming only when you really need to |
| **T.120** | 仅在真正需要时使用模板元编程 |
| **T.144** | Don't specialize function templates (overload instead) |
| **T.144** | 不要特化函数模板（改为重载） |

### Concepts (C++20)
### Concepts (C++20)
### 概念（C++20）

```cpp
#include <concepts>

// T.10 + T.11: Constrain templates with standard concepts
// T.10 + T.11: 使用标准概念约束模板
template<std::integral T>
T gcd(T a, T b) {
    while (b != 0) {
        a = std::exchange(b, a % b);
    }
    return a;
}

// T.13: Shorthand concept syntax
// T.13: 简写概念语法
void sort(std::ranges::random_access_range auto& range) {
    std::ranges::sort(range);
}

// Custom concept for domain-specific constraints
// 特定领域约束的自定义概念
template<typename T>
concept Serializable = requires(const T& t) {
    { t.serialize() } -> std::convertible_to<std::string>;
};

template<Serializable T>
void save(const T& obj, const std::string& path);
```

### Anti-Patterns
### Anti-Patterns
### 反模式

- Unconstrained templates in visible namespaces (T.47)
  - 在可见命名空间中的无约束模板（T.47）
- Specializing function templates instead of overloading (T.144)
  - 特化函数模板而非重载（T.144）
- Template metaprogramming where `constexpr` suffices (T.120)
  - 使用模板元编程而 `constexpr` 足够（T.120）
- `typedef` instead of `using` (T.43)
  - 使用 `typedef` 而非 `using`（T.43）

## Standard Library (SL.*)
## Standard Library (SL.*)
## 标准库（SL.*）

### Key Rules
### Key Rules
### 关键规则

| Rule | Summary |
|------|---------|
| **SL.1** | Use libraries wherever possible |
| **SL.1** | 尽可能使用库 |
| **SL.2** | Prefer the standard library to other libraries |
| **SL.2** | 优先使用标准库而非其他库 |
| **SL.con.1** | Prefer `std::array` or `std::vector` over C arrays |
| **SL.con.1** | 优先使用 `std::array` 或 `std::vector` 而非 C 数组 |
| **SL.con.2** | Prefer `std::vector` by default |
| **SL.con.2** | 默认优先使用 `std::vector` |
| **SL.str.1** | Use `std::string` to own character sequences |
| **SL.str.1** | 使用 `std::string` 拥有字符序列 |
| **SL.str.2** | Use `std::string_view` to refer to character sequences |
| **SL.str.2** | 使用 `std::string_view` 引用字符序列 |
| **SL.io.50** | Avoid `endl` (use `'\n'` -- `endl` forces a flush) |
| **SL.io.50** | 避免 `endl`（使用 `'\n'`——`endl` 强制刷新） |

```cpp
// SL.con.1 + SL.con.2: Prefer vector/array over C arrays
// SL.con.1 + SL.con.2: 优先使用 vector/array 而非 C 数组
const std::array<int, 4> fixed_data{1, 2, 3, 4};
std::vector<std::string> dynamic_data;

// SL.str.1 + SL.str.2: string owns, string_view observes
// SL.str.1 + SL.str.2: string 拥有，string_view 观察
std::string build_greeting(std::string_view name) {
    return "Hello, " + std::string(name) + "!";
}

// SL.io.50: Use '\n' not endl
// SL.io.50: 使用 '\n' 而不是 endl
std::cout << "result: " << value << '\n';
```

## Enumerations (Enum.*)
## Enumerations (Enum.*)
## 枚举（Enum.*）

### Key Rules
### Key Rules
### 关键规则

| Rule | Summary |
|------|---------|
| **Enum.1** | Prefer enumerations over macros |
| **Enum.1** | 优先使用枚举而非宏 |
| **Enum.3** | Prefer `enum class` over plain `enum` |
| **Enum.3** | 优先使用 `enum class` 而非普通 `enum` |
| **Enum.5** | Don't use ALL_CAPS for enumerators |
| **Enum.5** | 不要对枚举值使用全大写 |
| **Enum.6** | Avoid unnamed enumerations |
| **Enum.6** | 避免匿名枚举 |

```cpp
// Enum.3 + Enum.5: Scoped enum, no ALL_CAPS
// Enum.3 + Enum.5: 作用域枚举，不使用全大写
enum class Color { red, green, blue };
enum class LogLevel { debug, info, warning, error };

// BAD: plain enum leaks names, ALL_CAPS clashes with macros
// 错误：普通枚举泄漏名称，全大写与宏冲突
enum { RED, GREEN, BLUE };           // Enum.3 + Enum.5 + Enum.6 violation
#define MAX_SIZE 100                  // Enum.1 violation -- use constexpr
```

## Source Files & Naming (SF.*, NL.*)
## Source Files & Naming (SF.*, NL.*)
## 源文件和命名（SF.*、NL.*）

### Key Rules
### Key Rules
### 关键规则

| Rule | Summary |
|------|---------|
| **SF.1** | Use `.cpp` for code files and `.h` for interface files |
| **SF.1** | 对代码文件使用 `.cpp`，对接口文件使用 `.h` |
| **SF.7** | Don't write `using namespace` at global scope in a header |
| **SF.7** | 不要在头文件的全局作用域中写 `using namespace` |
| **SF.8** | Use `#include` guards for all `.h` files |
| **SF.8** | 为所有 `.h` 文件使用 `#include` 守卫 |
| **SF.11** | Header files should be self-contained |
| **SF.11** | 头文件应该是自包含的 |
| **NL.5** | Avoid encoding type information in names (no Hungarian notation) |
| **NL.5** | 避免在名称中编码类型信息（不要使用匈牙利命名法） |
| **NL.8** | Use a consistent naming style |
| **NL.8** | 使用一致的命名风格 |
| **NL.9** | Use ALL_CAPS for macro names only |
| **NL.9** | 仅对宏名使用全大写 |
| **NL.10** | Prefer `underscore_style` names |
| **NL.10** | 优先使用 `underscore_style` 名称 |

### Header Guard
### Header Guard
### 头文件守卫

```cpp
// SF.8: Include guard (or #pragma once)
// SF.8: 包含守卫（或 #pragma once）
#ifndef PROJECT_MODULE_WIDGET_H
#define PROJECT_MODULE_WIDGET_H

// SF.11: Self-contained -- include everything this header needs
// SF.11: 自包含——包含此头文件所需的一切
#include <string>
#include <vector>

namespace project::module {

class Widget {
public:
    explicit Widget(std::string name);
    const std::string& name() const;

private:
    std::string name_;
};

}  // namespace project::module

#endif  // PROJECT_MODULE_WIDGET_H
```

### Naming Conventions
### Naming Conventions
### 命名约定

```cpp
// NL.8 + NL.10: Consistent underscore_style
// NL.8 + NL.10: 一致的 underscore_style
namespace my_project {

constexpr int max_buffer_size = 4096;  // NL.9: not ALL_CAPS (it's not a macro)

class tcp_connection {                 // underscore_style class
public:
    void send_message(std::string_view msg);
    bool is_connected() const;

private:
    std::string host_;                 // trailing underscore for members
    int port_;
};

}  // namespace my_project
```

### Anti-Patterns
### Anti-Patterns
### 反模式

- `using namespace std;` in a header at global scope (SF.7)
  - 在头文件的全局作用域中使用 `using namespace std;`（SF.7）
- Headers that depend on inclusion order (SF.10, SF.11)
  - 依赖于包含顺序的头文件（SF.10、SF.11）
- Hungarian notation like `strName`, `iCount` (NL.5)
  - 像 `strName`、`iCount` 这样的匈牙利命名法（NL.5）
- ALL_CAPS for anything other than macros (NL.9)
  - 对宏以外的内容使用全大写（NL.9）

## Performance (Per.*)
## Performance (Per.*)
## 性能（Per.*）

### Key Rules
### Key Rules
### 关键规则

| Rule | Summary |
|------|---------|
| **Per.1** | Don't optimize without reason |
| **Per.1** | 不要无理由优化 |
| **Per.2** | Don't optimize prematurely |
| **Per.2** | 不要过早优化 |
| **Per.6** | Don't make claims about performance without measurements |
| **Per.6** | 不要在未经测量的情况下声称性能 |
| **Per.7** | Design to enable optimization |
| **Per.7** | 设计以实现优化 |
| **Per.10** | Rely on the static type system |
| **Per.10** | 依赖静态类型系统 |
| **Per.11** | Move computation from run time to compile time |
| **Per.11** | 将计算从运行时转移到编译时 |
| **Per.19** | Access memory predictably |
| **Per.19** | 可预测地访问内存 |

### Guidelines
### Guidelines
### 指南

```cpp
// Per.11: Compile-time computation where possible
// Per.11: 尽可能在编译时计算
constexpr auto lookup_table = [] {
    std::array<int, 256> table{};
    for (int i = 0; i < 256; ++i) {
        table[i] = i * i;
    }
    return table;
}();

// Per.19: Prefer contiguous data for cache-friendliness
// Per.19: 为提高缓存友好性优先使用连续数据
std::vector<Point> points;           // GOOD: contiguous
std::vector<std::unique_ptr<Point>> indirect_points; // BAD: pointer chasing
```

### Anti-Patterns
### Anti-Patterns
### 反模式

- Optimizing without profiling data (Per.1, Per.6)
  - 在没有性能分析数据的情况下优化（Per.1、Per.6）
- Choosing "clever" low-level code over clear abstractions (Per.4, Per.5)
  - 选择"聪明"的低级代码而非清晰的抽象（Per.4、Per.5）
- Ignoring data layout and cache behavior (Per.19)
  - 忽略数据布局和缓存行为（Per.19）

## Quick Reference Checklist
## Quick Reference Checklist
## 快速参考清单

Before marking C++ work complete:
## Before marking C++ work complete:
## 在标记 C++ 工作完成之前：

- [ ] No raw `new`/`delete` -- use smart pointers or RAII (R.11)
  - [ ] 没有裸 `new`/`delete`——使用智能指针或 RAII（R.11）
- [ ] Objects initialized at declaration (ES.20)
  - [ ] 对象在声明时初始化（ES.20）
- [ ] Variables are `const`/`constexpr` by default (Con.1, ES.25)
  - [ ] 变量默认是 `const`/`constexpr`（Con.1、ES.25）
- [ ] Member functions are `const` where possible (Con.2)
  - [ ] 成员函数在可能的情况下是 `const`（Con.2）
- [ ] `enum class` instead of plain `enum` (Enum.3)
  - [ ] 使用 `enum class` 而非普通 `enum`（Enum.3）
- [ ] `nullptr` instead of `0`/`NULL` (ES.47)
  - [ ] 使用 `nullptr` 而非 `0`/`NULL`（ES.47）
- [ ] No narrowing conversions (ES.46)
  - [ ] 没有窄化转换（ES.46）
- [ ] No C-style casts (ES.48)
  - [ ] 没有 C 风格转换（ES.48）
- [ ] Single-argument constructors are `explicit` (C.46)
  - [ ] 单参数构造函数是 `explicit`（C.46）
- [ ] Rule of Zero or Rule of Five applied (C.20, C.21)
  - [ ] 应用了零规则或五规则（C.20、C.21）
- [ ] Base class destructors are public virtual or protected non-virtual (C.35)
  - [ ] 基类析构函数是 public virtual 或 protected non-virtual（C.35）
- [ ] Templates are constrained with concepts (T.10)
  - [ ] 模板用概念约束（T.10）
- [ ] No `using namespace` in headers at global scope (SF.7)
  - [ ] 头文件全局作用域中没有 `using namespace`（SF.7）
- [ ] Headers have include guards and are self-contained (SF.8, SF.11)
  - [ ] 头文件有包含守卫且自包含（SF.8、SF.11）
- [ ] Locks use RAII (`scoped_lock`/`lock_guard`) (CP.20)
  - [ ] 锁使用 RAII（`scoped_lock`/`lock_guard`）（CP.20）
- [ ] Exceptions are custom types, thrown by value, caught by reference (E.14, E.15)
  - [ ] 异常是自定义类型，通过值抛出，通过引用捕获（E.14、E.15）
- [ ] `'\n'` instead of `std::endl` (SL.io.50)
  - [ ] 使用 `'\n'` 而非 `std::endl`（SL.io.50）
- [ ] No magic numbers (ES.45)
  - [ ] 没有幻数（ES.45）
