# UnitTest 源码文档

> 路径: `Source/Falcor/Testing/UnitTest.h` + `UnitTest.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Testing (测试框架)

## 功能概述

Falcor 的单元测试框架，支持 CPU 测试和 GPU 测试。CPU 测试在主机端执行普通 C++ 代码；GPU 测试通过计算着色器在 GPU 上执行，并将结果读回主机进行验证。框架提供了类似 Google Test 的断言宏（`EXPECT_EQ`、`ASSERT_TRUE` 等）、测试注册机制、标签过滤、并行执行和 JUnit XML 报告生成。

## 类与接口

### `UnitTestContext`

- **继承**: 无
- **职责**: 测试上下文基类，管理失败报告。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `void skip(const char* message)` | 运行时跳过当前测试 |
| `void reportFailure(const std::string& message)` | 报告测试失败 |
| `std::vector<std::string> getFailureMessages() const` | 获取所有失败消息 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mNumFailures` | `int` | 失败计数 |
| `mFailureMessages` | `std::vector<std::string>` | 失败消息列表 |

### `CPUUnitTestContext`

- **继承**: `UnitTestContext`
- **职责**: CPU 测试上下文（当前无额外功能）。

### `GPUUnitTestContext`

- **继承**: `UnitTestContext`
- **职责**: GPU 测试上下文，管理计算着色器的创建、变量绑定和执行。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `void createProgram(const std::filesystem::path&, const std::string& csEntry, ...)` | 从文件创建计算着色器程序 |
| `void createProgram(const ProgramDesc&, const DefineList&, bool createShaderVars)` | 从程序描述创建计算着色器 |
| `void createVars()` | 创建/重建着色器变量 |
| `ProgramVars& vars()` | 获取程序变量 |
| `ShaderVar operator[](const std::string& name)` | 按名称访问着色器变量 |
| `void allocateStructuredBuffer(const std::string& name, uint32_t nElements, ...)` | 分配结构化缓冲区 |
| `std::vector<T> readBuffer(const char* bufferName)` | 读取结构化缓冲区内容 |
| `void runProgram(const uint3& dimensions)` | 以指定线程数执行计算着色器 |
| `void runProgram(uint32_t width, uint32_t height, uint32_t depth)` | 以指定维度执行计算着色器 |
| `const ref<Device>& getDevice() const` | 获取 GPU 设备 |
| `RenderContext* getRenderContext() const` | 获取渲染上下文 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `ref<Device>` | GPU 设备 |
| `mpState` | `ref<ComputeState>` | 计算状态 |
| `mpProgram` | `ref<Program>` | 着色器程序 |
| `mpVars` | `ref<ProgramVars>` | 程序变量 |
| `mThreadGroupSize` | `uint3` | 线程组大小 |
| `mStructuredBuffers` | `std::map<std::string, ref<Buffer>>` | 已分配的结构化缓冲区 |

### `Test`（结构体）

| 成员 | 类型 | 说明 |
|------|------|------|
| `suiteName` | `std::string` | 测试套件名（源文件名） |
| `name` | `std::string` | 测试名称 |
| `tags` | `std::set<std::string>` | 标签集合 |
| `skipMessage` | `std::string` | 跳过原因（非空则跳过） |
| `deviceType` | `Device::Type` | GPU 设备类型 |
| `cpuFunc` / `gpuFunc` | 函数对象 | CPU/GPU 测试函数 |

### `RunOptions`（结构体）

| 成员 | 类型 | 说明 |
|------|------|------|
| `deviceDesc` | `Device::Desc` | GPU 设备描述 |
| `testSuiteFilter` | `std::string` | 测试套件名过滤正则 |
| `testCaseFilter` | `std::string` | 测试用例名过滤正则 |
| `tagFilter` | `std::string` | 标签过滤（逗号分隔，`-`/`!`/`~` 排除，`+` 包含） |
| `xmlReportPath` | `std::filesystem::path` | JUnit XML 报告输出路径 |
| `parallel` | `uint32_t` | 并行测试线程数 |
| `repeat` | `uint32_t` | 测试重复次数 |

### 异常类

| 类名 | 说明 |
|------|------|
| `TooManyFailedTestsException` | 失败次数超过上限（25 次） |
| `ErrorRunningTestException` | 测试运行时错误 |
| `AssertingTestException` | ASSERT 断言失败（中止当前测试） |
| `SkippingTestException` | 测试被跳过 |

### `StreamSink`

- **职责**: 累积测试失败消息的流式输出工具。析构时将消息传递给 `reportFailure()`。

### 辅助配置结构体

| 结构体 | 说明 |
|--------|------|
| `Tags` | 测试标签配置 |
| `Skip` | 测试跳过配置 |
| `DeviceTypes` | GPU 设备类型限制 |
| `Options` | 综合配置选项 |

## 宏定义

### 测试注册宏

| 宏 | 说明 |
|----|------|
| `CPU_TEST(name, ...)` | 定义 CPU 单元测试。自动添加 `cpu` 标签 |
| `GPU_TEST(name, ...)` | 定义 GPU 单元测试。自动添加 `gpu` 标签 |
| `TAGS(...)` | 添加测试标签 |
| `SKIP(msg)` | 标记测试为跳过 |
| `DEVICE_TYPES(...)` | 限制 GPU 测试的设备类型 |

### 断言宏

| 宏 | 说明 |
|----|------|
| `EXPECT_TRUE/FALSE(expr)` | 非致命布尔断言 |
| `ASSERT_TRUE/FALSE(expr)` | 致命布尔断言（失败则中止测试） |
| `EXPECT_EQ/NE/LT/GT/LE/GE(lhs, rhs)` | 非致命比较断言 |
| `ASSERT_EQ/NE/LT/GT/LE/GE(lhs, rhs)` | 致命比较断言 |
| `EXPECT_THROW(expr)` | 期望表达式抛出异常 |
| `EXPECT_THROW_AS(expr, type)` | 期望表达式抛出指定类型异常 |
| `*_MSG(...)` | 各断言的带自定义消息版本 |

## 全局函数

| 函数签名 | 说明 |
|----------|------|
| `int32_t runTests(const RunOptions&)` | 运行所有测试，返回失败数 |
| `std::vector<Test> enumerateTests()` | 枚举所有已注册测试 |
| `std::vector<Test> filterTests(...)` | 按条件过滤测试 |
| `void registerCPUTest(...)` | 注册 CPU 测试 |
| `void registerGPUTest(...)` | 注册 GPU 测试 |

## 依赖关系

### 本文件引用

- `Core/Error.h` - 错误处理
- `Core/State/ComputeState.h` - 计算状态
- `Core/Program/Program.h` - 着色器程序
- `Core/Program/ProgramVars.h` - 程序变量
- `Core/API/Device.h` - GPU 设备
- `Core/API/RenderContext.h` - 渲染上下文
- `Utils/Threading.h` - 多线程工具
- `Utils/Scripting/Scripting.h` - 脚本引擎
- `pugixml.hpp` - XML 生成
- `BS_thread_pool_light.hpp` - 线程池

### 被以下文件引用

- 所有单元测试源文件

## 实现细节

- **测试注册**: 使用静态全局对象的构造函数在 `main()` 前自动注册测试到全局注册表 (`getTestRegistry()`)。
- **GPU 测试设备池**: `DevicePool` 管理 GPU 设备的获取和释放，支持并行测试时的设备复用，使用互斥锁保证线程安全。
- **执行模式**:
  - 串行模式（`parallel=1`）：按套件分组执行，支持重复运行和 XML 报告。
  - 并行模式（`parallel>1`）：使用线程池并行执行测试，支持 Ctrl-C 中断。
- **GPU 测试流程**: `createProgram()` -> 可选 `allocateStructuredBuffer()` -> 设置变量 -> `runProgram()` -> `readBuffer()` -> 断言验证。
- **线程组大小**: 自动从着色器反射中获取 `[numthreads]` 声明的线程组大小，`runProgram()` 根据请求的总线程数自动计算 dispatch 的组数。
- **失败上限**: 单个测试最多报告 25 次失败后自动中止（`kMaxTestFailures`）。
- **内置自测试**: 文件末尾包含 `TestCPUTest`、`TestSingleEval`、`TestGPUTest` 等自测试用例。
