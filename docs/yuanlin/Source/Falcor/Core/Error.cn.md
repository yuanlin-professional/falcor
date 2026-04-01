# Error 源码文档

> 路径: `Source/Falcor/Core/Error.h` + `Source/Falcor/Core/Error.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core

## 功能概述

定义 Falcor 框架的异常体系和错误诊断机制。包括自定义异常类、异常抛出宏（`FALCOR_THROW`、`FALCOR_CHECK`）、断言宏（`FALCOR_ASSERT`）以及错误报告函数。支持可配置的调试器中断、堆栈跟踪附加和消息框显示等诊断行为。

## 类与接口

### `Exception`
- **继承**: `std::exception`
- **职责**: Falcor 所有异常的基类

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `mpWhat` | `std::shared_ptr<std::string>` | 引用计数的错误消息，使拷贝构造函数为 noexcept |

### `RuntimeError`
- **继承**: `Exception`
- **职责**: 运行时错误异常，由 `FALCOR_THROW` 抛出

### `AssertionError`
- **继承**: `Exception`
- **职责**: 断言失败异常，由 `FALCOR_ASSERT` 抛出

## 枚举类型

### `ErrorDiagnosticFlags`
错误诊断行为标志（位标志枚举）。

| 值 | 说明 |
|------|------|
| `None` | 无诊断 |
| `BreakOnThrow` | 抛出异常时中断调试器 |
| `BreakOnAssert` | 断言失败时中断调试器 |
| `AppendStackTrace` | 异常消息中附加堆栈跟踪 |
| `ShowMessageBoxOnError` | 错误报告时显示消息框 |

## 宏定义

| 宏 | 说明 |
|------|------|
| `FALCOR_THROW(...)` | 抛出 `RuntimeError` 异常，支持格式化字符串 |
| `FALCOR_CHECK(cond, ...)` | 条件不满足时抛出异常 |
| `FALCOR_UNIMPLEMENTED()` | 标记未实现的函数 |
| `FALCOR_UNREACHABLE()` | 标记不可达代码 |
| `FALCOR_ASSERT(cond, ...)` | 断言，仅在 `FALCOR_ENABLE_ASSERTS` 启用时有效 |
| `FALCOR_ASSERT_EQ/NE/GE/GT/LE/LT(a, b)` | 二元比较断言，自动打印比较值 |

## 全局函数

| 函数签名 | 说明 |
|----------|------|
| `setErrorDiagnosticFlags(flags)` | 设置全局错误诊断标志 |
| `getErrorDiagnosticFlags()` | 获取全局错误诊断标志 |
| `reportErrorAndContinue(msg)` | 记录错误日志，可选显示消息框 |
| `reportErrorAndAllowRetry(msg)` | 记录错误并显示重试/中止消息框 |
| `reportFatalErrorAndTerminate(msg)` | 报告致命错误并终止应用程序 |
| `catchAndReportAllExceptions(callback)` | 捕获所有异常并报告（用于 main 函数） |

## 依赖关系

### 本文件引用
- `Core/Macros.h` — 基础宏定义
- `Core/Platform/OS.h` — `getStackTrace`、`isDebuggerPresent`、`debugBreak`、`msgBox`
- `Utils/Logger.h` — 日志记录
- `fstd/source_location.h` — 源码位置信息
- `fmt/format.h` — 格式化

### 被以下文件引用
- 框架中几乎所有文件均依赖此模块进行错误处理

## 实现细节

- 全局诊断标志默认启用 `BreakOnThrow | BreakOnAssert`。
- `throwException` 会在消息中附加源文件位置信息，并根据标志选择性附加堆栈跟踪和中断调试器。
- `reportFatalErrorAndTerminate` 使用 `std::atomic<bool>` 防止重入，并通过 `std::quick_exit(1)` 立即终止。
- 异常类通过 `FALCOR_SCRIPT_BINDING` 注册到 Python。
