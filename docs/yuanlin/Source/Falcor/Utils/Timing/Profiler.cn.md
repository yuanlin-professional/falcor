# Profiler 源码文档

> 路径: `Source/Falcor/Utils/Timing/Profiler.h` + `Source/Falcor/Utils/Timing/Profiler.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/Timing

## 功能概述

CPU/GPU 性能分析器，支持嵌套事件层级、双缓冲 GPU 计时（避免 GPU 停顿）、EMA 平滑平均、统计信息（最小/最大/均值/标准差）计算、性能数据捕获与导出。通过 `FALCOR_PROFILE` 宏和 RAII 封装的 `ScopedProfilerEvent` 简化使用。

## 类与接口

### `Profiler`

- **导出宏**: `FALCOR_API`
- **职责**: 管理 CPU 和 GPU 性能事件的计时、层级组织和数据收集

#### 枚举

| 枚举 | 值 | 说明 |
|------|----|------|
| `Flags::None` | `0x0` | 无标志 |
| `Flags::Internal` | `0x1` | 启用内部性能记录 |
| `Flags::Pix` | `0x2` | 启用 PIX/调试事件标记 |
| `Flags::Default` | `Internal \| Pix` | 默认同时启用两者 |

#### 内部类 `Stats`

| 成员 | 类型 | 说明 |
|------|------|------|
| `min` | `float` | 最小值 |
| `max` | `float` | 最大值 |
| `mean` | `float` | 均值 |
| `stdDev` | `float` | 标准差 |
| `static Stats compute(const float* data, size_t len)` | — | 从数据数组计算统计信息 |

#### 内部类 `Event`

- **职责**: 表示一个性能分析事件，管理 CPU/GPU 计时数据

| 方法 | 说明 |
|------|------|
| `getName()` | 获取嵌套事件名称（路径格式，如 `/RenderPass/Shadow`） |
| `getCpuTime() / getGpuTime()` | 获取上一帧的 CPU/GPU 时间（毫秒） |
| `getCpuTimeAverage() / getGpuTimeAverage()` | 获取 EMA 平滑后的平均时间 |
| `computeCpuTimeStats() / computeGpuTimeStats()` | 计算历史时间的统计信息 |
| `resetStats()` | 重置统计历史 |

| 关键成员 | 说明 |
|----------|------|
| `mFrameData[2]` | 双缓冲帧数据，包含 CPU 开始时间、累计 CPU 时间、GPU 计时器池 |
| `mCpuTimeHistory / mGpuTimeHistory` | 环形缓冲区，最大 512 条记录 |

#### 内部类 `Capture`

- **职责**: 捕获多帧性能数据用于事后分析

| 方法 | 说明 |
|------|------|
| `getFrameCount()` | 获取捕获的帧数 |
| `getLanes()` | 获取所有数据通道 |
| `toJsonString()` | 序列化为 JSON 字符串 |
| `writeToFile(path)` | 写入 JSON 文件 |

#### Profiler 关键方法

| 方法签名 | 说明 |
|----------|------|
| `Profiler(ref<Device> pDevice)` | 构造函数，创建 GPU Fence |
| `bool isEnabled() const` | 检查是否启用 |
| `void setEnabled(bool enabled)` | 启用/禁用 |
| `bool isPaused() const` | 检查是否暂停 |
| `void setPaused(bool paused)` | 暂停/恢复 |
| `void startEvent(RenderContext*, const std::string& name, Flags)` | 开始一个性能事件 |
| `void endEvent(RenderContext*, const std::string& name, Flags)` | 结束一个性能事件 |
| `Event* getEvent(const std::string& name)` | 获取或创建事件 |
| `const std::vector<Event*>& getEvents() const` | 获取上一帧的事件列表 |
| `void endFrame(RenderContext*)` | 结束当前帧的性能分析，必须每帧调用一次 |
| `void startCapture(size_t reservedFrames = 1024)` | 开始性能数据捕获 |
| `std::shared_ptr<Capture> endCapture()` | 结束捕获并返回数据 |
| `bool isCapturing() const` | 检查是否正在捕获 |
| `void resetStats()` | 在下一次 `endFrame` 时重置统计数据 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `BreakableReference<Device>` | GPU 设备引用 |
| `mEvents` | `std::unordered_map<std::string, std::shared_ptr<Event>>` | 事件名到事件对象的映射 |
| `mCurrentFrameEvents` | `std::vector<Event*>` | 当前帧注册的事件 |
| `mLastFrameEvents` | `std::vector<Event*>` | 上一帧的事件列表 |
| `mCurrentEventName` | `std::string` | 当前嵌套事件路径 |
| `mFrameIndex` | `uint32_t` | 当前帧索引（用于双缓冲） |
| `mpFence` | `ref<Fence>` | GPU 同步 Fence |

---

### `ScopedProfilerEvent`

- **导出宏**: `FALCOR_API`
- **职责**: RAII 封装，构造时调用 `startEvent`，析构时调用 `endEvent`

---

### 宏定义

| 宏 | 说明 |
|----|------|
| `FALCOR_PROFILE(_pRenderContext, _name)` | 在当前作用域创建 `ScopedProfilerEvent`，`FALCOR_ENABLE_PROFILER` 未定义时为空操作 |
| `FALCOR_PROFILE_CUSTOM(_pRenderContext, _name, _flags)` | 带自定义标志的版本 |

## 依赖关系

### 本文件引用

- `CpuTimer.h` — CPU 端计时
- `Core/Macros.h` — 宏定义
- `Core/API/GpuTimer.h` — GPU 计时器
- `Core/API/Fence.h` — GPU 同步
- `Core/API/Device.h` — 设备接口
- `Utils/Logger.h` — 日志
- `Utils/Scripting/ScriptBindings.h` — Python 绑定

### 被以下文件引用

- `ProfilerUI.h` — 可视化 Profiler 数据
- 各渲染 Pass 通过 `FALCOR_PROFILE` 宏使用

## 实现细节

- **双缓冲**: 每个 `Event` 维护 `mFrameData[2]`，当前帧写入一个缓冲区，读取上一帧另一个缓冲区的结果，通过帧索引的奇偶切换实现，避免 GPU 计时器回读时的停顿。
- **EMA 平滑**: 使用 `sigma=0.98` 的指数移动平均，100 帧后某个值的贡献降至约 1.7%。
- **GPU 同步**: `endFrame` 使用单个 `Fence` 等待上一帧的 GPU 计时器完成（而非每个事件一个 Fence），然后批量回读所有事件的 GPU 时间。
- **嵌套路径**: 事件名称使用 `/` 分隔的路径格式（如 `/RenderPass/Shadow/CSM`），`startEvent` 追加、`endEvent` 截断。
- **Python 绑定**: 导出 `enabled`、`paused`、`is_capturing`、`events` 属性和 `start_capture`、`end_capture`、`reset_stats` 方法。还提供 `ProfilerEvent` Python 上下文管理器（`with` 语法）。
- **PIX 集成**: 当 `Flags::Pix` 启用时，同时在渲染上下文中插入调试事件标记，便于 PIX 等 GPU 调试工具分析。
