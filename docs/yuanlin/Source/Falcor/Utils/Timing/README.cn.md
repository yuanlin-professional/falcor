# Timing (计时) 模块

## 功能概述

Timing 模块为 Falcor 渲染框架提供完整的时间管理与性能分析基础设施。该模块覆盖以下核心能力：

- **应用时钟**：`Clock` 类提供实时模式与固定帧率模拟两种时钟模式，支持暂停、步进、时间缩放、时间循环及退出条件判定。
- **CPU 计时器**：`CpuTimer` 基于 `std::chrono::high_resolution_clock` 提供高精度 CPU 时间测量。
- **GPU 计时器**：`GpuTimer`（Slang 着色器端）通过 NVAPI 全局计时器提供 GPU 侧时间测量。
- **帧率统计**：`FrameRate` 维护滑动窗口（60 帧）计算平均帧时间与 FPS。
- **性能分析器**：`Profiler` 实现 CPU/GPU 双缓冲分层事件性能分析，支持统计信息、数据捕获及 JSON 导出。
- **分析器界面**：`ProfilerUI` 通过 ImGui 可视化性能分析数据，包含事件树、时间图表和高亮交互。
- **时间报告**：`TimeReport` 用于记录和输出 CPU 长时间任务的耗时测量。

## 架构图

```mermaid
classDiagram
    class Clock {
        +setTime(seconds) Clock&
        +getTime() double
        +getDelta() double
        +tick() Clock&
        +setFramerate(fps) Clock&
        +pause() / play() / stop()
        +step(frames) Clock&
        +setTimeScale(scale)
        +shouldExit() bool
        +renderUI(Window&)
        -Time mRealtime, mTime
        -CpuTimer mTimer
    }

    class CpuTimer {
        +getCurrentTimePoint() TimePoint
        +update() TimePoint
        +delta() double
        +calcDuration(start, end) double
    }

    class FrameRate {
        +reset()
        +newFrame()
        +getAverageFrameTime() double
        +getLastFrameTime() double
        +getFrameCount() uint64_t
        +getMsg() string
        -Clock mClock
    }

    class Profiler {
        +startEvent(ctx, name, flags)
        +endEvent(ctx, name, flags)
        +endFrame(ctx)
        +startCapture() / endCapture()
        +getEvents() vector~Event*~
        +getEvent(name) Event*
        -map~string, Event~ mEvents
    }

    class Event {
        +getCpuTime() float
        +getGpuTime() float
        +computeCpuTimeStats() Stats
        +computeGpuTimeStats() Stats
        -FrameData[2] 双缓冲
    }

    class ProfilerUI {
        +render()
        -renderOptions()
        -renderGraph()
        -updateEventData()
        -Profiler* mpProfiler
        -GraphMode mGraphMode
    }

    class TimeReport {
        +reset() / resetTimer()
        +measure(name)
        +addTotal(name)
        +printToLog()
    }

    class GpuTimer_Slang {
        +start()
        +getElapsed() uint
        +getCurrentTimePoint() TimePoint
        +calcDuration(start, end) uint
    }

    Clock --> CpuTimer : 使用
    FrameRate --> Clock : 内嵌
    Profiler *-- Event : 管理
    Profiler --> CpuTimer : CPU 计时
    Profiler --> "Core::GpuTimer" : GPU 计时
    ProfilerUI --> Profiler : 可视化
    TimeReport --> CpuTimer : 使用
```

## 文件清单

| 文件名 | 类型 | 说明 |
|--------|------|------|
| `Clock.h` | 头文件 | 应用时钟类，支持实时/固定帧率模式、暂停/步进/时间缩放/循环/退出条件 |
| `Clock.cpp` | 实现 | `Clock` 的 tick 逻辑、UI 渲染、脚本导出实现 |
| `CpuTimer.h` | 头文件 | 高精度 CPU 计时器，基于 `high_resolution_clock`，提供 `update()`/`delta()`/`calcDuration()` |
| `FrameRate.h` | 头文件 | 帧率计算辅助类，60 帧滑动窗口计算平均帧时间 |
| `FrameRate.cpp` | 实现 | `FrameRate::getMsg()` FPS 消息格式化 |
| `Profiler.h` | 头文件 | CPU/GPU 性能分析器，双缓冲事件层次结构、统计信息与数据捕获 |
| `Profiler.cpp` | 实现 | 事件开始/结束、帧结束处理、捕获序列化逻辑 |
| `ProfilerUI.h` | 头文件 | 性能分析器 ImGui 界面，事件列表与时间图表可视化 |
| `ProfilerUI.cpp` | 实现 | ImGui 绘制逻辑，图表渲染与事件数据更新 |
| `TimeReport.h` | 头文件 | CPU 长任务耗时记录与报告工具 |
| `TimeReport.cpp` | 实现 | 测量记录与日志输出 |
| `GpuTimer.slang` | Slang 着色器 | GPU 侧计时器，基于 NVAPI 全局计时器低 32 位实现 |

## 依赖关系

### 内部依赖
- `Core/Macros.h` -- 导出宏、断言宏
- `Core/API/GpuTimer.h` -- GPU 计时器 API 封装（`Profiler` 使用）
- `Core/API/Fence.h` -- GPU 同步栅栏（`Profiler` 使用）
- `Utils/UI/Gui.h` -- UI 控件（`Clock::renderUI()` 使用）
- `Utils/NVAPI.slangh` -- NVAPI 着色器接口（`GpuTimer.slang` 使用）

### 外部依赖
- **std::chrono** -- C++ 标准高精度计时（`CpuTimer`）
- **ImGui** -- 即时模式 GUI 库（`ProfilerUI` 直接调用）

## 关键类与接口

### `Clock`
应用级时钟，每帧调用 `tick()` 推进时间。两种模式：
- **实时模式**：`mFramerate == 0` 时基于 `CpuTimer` 的实际流逝时间
- **帧率模拟**：`setFramerate(fps)` 后每次 tick 固定步进 `1/fps` 秒

支持 `setTime()` / `setFrame()` 跳转，`setStartTime()` / `setEndTime()` 循环，`setExitTime()` / `setExitFrame()` 退出条件。

### `CpuTimer`
轻量级 CPU 计时器，`update()` 记录当前时间点并计算与上次调用的时间差（秒），`calcDuration()` 静态方法计算两时间点间的毫秒数。

### `Profiler`
核心性能分析器，采用双缓冲避免 GPU 刷新。关键接口：
- `startEvent()` / `endEvent()` -- 配对调用，自动构建事件层次
- `FALCOR_PROFILE(ctx, name)` -- RAII 宏，通过 `ScopedProfilerEvent` 自动配对
- `startCapture()` / `endCapture()` -- 数据捕获，可导出为 JSON

### `ProfilerUI`
基于 ImGui 的性能可视化，支持 CPU/GPU 时间图表模式切换、事件高亮及 EMA 平均显示。

### `TimeReport`
简单的 CPU 任务计时器，通过 `measure(name)` 记录各阶段耗时，`printToLog()` 输出到日志。
