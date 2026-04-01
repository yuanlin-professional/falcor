# CpuTimer 源码文档

> 路径: `Source/Falcor/Utils/Timing/CpuTimer.h`
> 类型: C++ 头文件（仅头文件）
> 模块: Utils/Timing

## 功能概述

CPU 高精度计时器，封装了 `std::chrono::high_resolution_clock`，为应用程序提供时间点获取、间隔测量和耗时计算等基础计时能力。

## 类与接口

### `CpuTimer`

- **命名空间**: `Falcor`
- **职责**: 提供 CPU 端的高精度时间测量工具

#### 类型定义

| 类型 | 说明 |
|------|------|
| `TimePoint` | `std::chrono::time_point<std::chrono::high_resolution_clock>`，时间点类型 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static TimePoint getCurrentTimePoint()` | 获取当前时间点 |
| `TimePoint update()` | 更新计时器，记录与上次更新的时间差，返回当前时间点 |
| `double delta() const` | 获取上次 `update()` 到前一次 `update()` 之间的时间差（秒） |
| `static double calcDuration(TimePoint start, TimePoint end)` | 计算两个时间点之间的时长（毫秒） |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mCurrentTime` | `TimePoint` | 上次 `update()` 时记录的时间点 |
| `mElapsedTime` | `std::chrono::duration<double>` | 上次两次 `update()` 之间的时间间隔（秒） |

## 依赖关系

### 本文件引用

- `<chrono>` — C++ 标准计时库

### 被以下文件引用

- `Clock.h` — `Clock` 类使用 `CpuTimer` 进行实时时间测量
- `Profiler.h` — `Profiler::Event` 使用 `CpuTimer::TimePoint` 记录事件开始时间
- `TimeReport.h` — `TimeReport` 使用 `CpuTimer::TimePoint` 进行测量
- `FrameRate.h` — 间接通过 `Clock` 使用

## 实现细节

- **全部内联**: 所有方法在头文件中内联实现，无运行时开销。
- **精度注意**: `delta()` 返回秒为单位的 `double` 值，而 `calcDuration()` 返回毫秒为单位的 `double` 值（通过纳秒精度中间转换实现）。两者返回单位不同，使用时需注意。
