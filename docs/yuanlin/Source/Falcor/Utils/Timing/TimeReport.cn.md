# TimeReport 源码文档

> 路径: `Source/Falcor/Utils/Timing/TimeReport.h` + `Source/Falcor/Utils/Timing/TimeReport.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/Timing

## 功能概述

CPU 端的时间测量报告工具类，用于记录一系列命名的时间测量并打印到日志。主要面向较长运行时间的 CPU 任务（如场景加载、资源初始化等），提供简洁的 measure/log 工作流。

## 类与接口

### `TimeReport`

- **导出宏**: `FALCOR_API`
- **职责**: 记录多段 CPU 耗时并输出格式化的时间报告

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `TimeReport()` | 构造函数，调用 `reset()` 初始化 |
| `void reset()` | 重置内部计时器和所有已记录的测量数据 |
| `void resetTimer()` | 仅重置计时器起点，保留已有测量记录 |
| `void printToLog()` | 将所有测量记录打印到日志文件，包含任务名、耗时和占总时间百分比 |
| `void measure(const std::string& name)` | 记录一次时间测量，测量从上次 `reset()`/`measure()` 到当前的耗时 |
| `void addTotal(const std::string name = "Total")` | 添加一条"总计"记录，值为所有测量的耗时之和 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mLastMeasureTime` | `CpuTimer::TimePoint` | 上次测量的时间点 |
| `mMeasurements` | `std::vector<std::pair<std::string, double>>` | 测量记录列表（名称, 秒数） |
| `mTotal` | `double` | 总计耗时（秒） |

## 依赖关系

### 本文件引用

- `CpuTimer.h` — 使用 `CpuTimer::TimePoint` 和 `getCurrentTimePoint()`
- `Core/Macros.h` — 宏定义
- `Utils/Logger.h` — `logInfo` 输出日志
- `Utils/StringUtils.h` — `padStringToLength` 格式化对齐

### 被以下文件引用

- 场景加载、资源初始化等需要性能分析的长时间 CPU 操作

## 实现细节

- **增量测量**: 每次 `measure()` 计算当前时间点与 `mLastMeasureTime` 的差值，然后更新 `mLastMeasureTime`，实现链式增量测量（A -> B -> C 各段分别记录）。
- **日志格式**: `printToLog` 将任务名填充到 25 字符宽度，后跟耗时秒数。如果已调用 `addTotal`，则额外显示每项占总时间的百分比。
- **总计计算**: `addTotal` 使用 `std::accumulate` 求和所有已有测量的耗时，并将总计也作为一条记录添加到列表末尾。
