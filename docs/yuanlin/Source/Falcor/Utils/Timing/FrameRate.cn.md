# FrameRate 源码文档

> 路径: `Source/Falcor/Utils/Timing/FrameRate.h` + `Source/Falcor/Utils/Timing/FrameRate.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/Timing

## 功能概述

帧率计算辅助类，通过滑动窗口（最近 60 帧）统计平均帧时间，并据此计算 FPS。每帧调用 `newFrame()` 记录帧时间，支持重置统计数据和获取格式化 FPS 信息。

## 类与接口

### `FrameRate`

- **导出宏**: `FALCOR_API`
- **职责**: 计算和报告应用程序的帧率

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `FrameRate()` | 构造函数，分配 60 帧大小的滑动窗口并重置 |
| `void reset()` | 重置帧计数和内部时钟，模拟应用刚启动的状态 |
| `void newFrame()` | 记录一帧，应每帧调用一次 |
| `double getAverageFrameTime() const` | 获取滑动窗口内的平均帧时间（秒） |
| `double getLastFrameTime() const` | 获取最近一帧的渲染时间（秒） |
| `uint64_t getFrameCount() const` | 获取总帧计数 |
| `std::string getMsg(bool vsyncOn = false) const` | 获取格式化的 FPS 消息字符串 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mClock` | `Clock` | 内部时钟实例，用于测量帧间实时时间差 |
| `mFrameTimes` | `std::vector<double>` | 帧时间滑动窗口数组 |
| `mFrameCount` | `uint64_t` | 总帧计数 |
| `kFrameWindow` | `static constexpr uint64_t` | 滑动窗口大小，固定为 60 |

#### 自由函数

| 函数签名 | 说明 |
|----------|------|
| `std::string to_string(const FrameRate& fr, bool vsyncOn = false)` | 内联转换函数，调用 `fr.getMsg()` |

## 依赖关系

### 本文件引用

- `Clock.h` — 使用 `Clock` 获取实时帧间隔
- `Core/Macros.h` — 宏定义
- `fmt/core.h` — 格式化输出

### 被以下文件引用

- 应用主循环用于显示帧率信息

## 实现细节

- **滑动窗口**: 使用环形缓冲区（`mFrameCount % kFrameWindow` 作为写入索引）存储最近 60 帧的帧时间，`getAverageFrameTime` 取窗口内已有帧数的平均值。
- **测量方式**: 每次 `newFrame()` 调用 `mClock.tick().getRealTimeDelta()` 获取真实帧间隔时间，然后立即重置时钟以开始下一帧的测量。
- **消息格式**: `getMsg` 输出格式为 `"XX.X FPS (XX.X ms/frame)"`，VSync 开启时追加 `", VSync"` 后缀。
