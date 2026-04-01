# Logger 源码文档

> 路径: `Source/Falcor/Utils/Logger.h` + `Source/Falcor/Utils/Logger.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils

## 功能概述

提供 Falcor 框架的日志系统。支持多级日志（Fatal/Error/Warning/Info/Debug）、多输出目标（控制台/文件/调试窗口）以及消息去重功能。同时提供了 Python 脚本绑定。

## 类与接口

### `Logger`

- **继承**: 无（静态类，构造函数删除）
- **职责**: 全局日志管理，控制日志级别、输出目标和日志文件

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static void shutdown()` | 关闭日志系统，关闭日志文件 |
| `static void setVerbosity(Level level)` | 设置日志详细级别 |
| `static Level getVerbosity()` | 获取当前日志级别 |
| `static void setOutputs(OutputFlags outputs)` | 设置日志输出目标 |
| `static OutputFlags getOutputs()` | 获取当前输出目标 |
| `static void setLogFilePath(const path& path)` | 设置日志文件路径 |
| `static path getLogFilePath()` | 获取日志文件路径 |
| `static void log(Level level, string_view msg, Frequency freq)` | 记录日志消息 |

### `Logger::Level` 枚举

| 值 | 说明 |
|----|------|
| `Disabled` | 禁用日志 |
| `Fatal` | 致命错误 |
| `Error` | 错误 |
| `Warning` | 警告 |
| `Info` | 信息 |
| `Debug` | 调试 |

### `Logger::OutputFlags` 枚举

| 值 | 说明 |
|----|------|
| `None` | 无输出 |
| `Console` | 输出到控制台 |
| `File` | 输出到日志文件 |
| `DebugWindow` | 输出到调试窗口 |

### 便捷日志函数

| 函数 | 说明 |
|------|------|
| `logDebug(...)` | 记录调试消息 |
| `logInfo(...)` | 记录信息消息 |
| `logWarning(...)` / `logWarningOnce(...)` | 记录警告（可去重） |
| `logError(...)` / `logErrorOnce(...)` | 记录错误（可去重） |
| `logFatal(...)` | 记录致命错误 |

## 依赖关系

### 本文件引用

- `Core/Macros.h` — 宏定义
- `Utils/StringFormatters.h` — 格式化工具
- `fmt/core.h` — 格式化库
- `Core/Error.h`、`Core/Platform/OS.h` — 错误处理和平台抽象
- `Utils/Scripting/ScriptBindings.h` — Python 绑定

### 被以下文件引用

- 几乎所有 Falcor 模块（通用日志基础设施）

## 实现细节

- 所有操作通过全局互斥锁 `sMutex` 保护，线程安全
- `MessageDeduplicator` 单例类使用 `std::set` 存储已输出的消息字符串，实现去重
- 日志文件延迟创建，首次写入时才打开
- Error 及以下级别输出到 `stderr`，Info 及以上输出到 `stdout`
- `FALCOR_PRINT` 宏用于快速调试输出变量名和值
- 提供完整的 pybind11 绑定，支持从 Python 控制日志系统
