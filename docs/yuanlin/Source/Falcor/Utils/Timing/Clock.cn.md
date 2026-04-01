# Clock 源码文档

> 路径: `Source/Falcor/Utils/Timing/Clock.h` + `Source/Falcor/Utils/Timing/Clock.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/Timing

## 功能概述

Falcor 时钟系统，同时支持实时时钟模式（基于系统时钟）和固定帧率模拟模式（基于 tick 计数）。提供时间设置、帧率控制、时间缩放、暂停/恢复/步进、时间循环、退出条件设置等功能。内置 GUI 渲染和 Python 脚本绑定。

## 类与接口

### `Clock`

- **导出宏**: `FALCOR_API`
- **职责**: 管理应用程序时间流逝，支持实时和模拟帧率两种模式

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `Clock()` | 构造函数，初始化时间为 0 |
| `Clock& setTime(double seconds, bool deferToNextTick = false)` | 设置当前时间（秒），可延迟到下一次 tick 生效 |
| `double getTime() const` | 获取上次 `tick()` 调用时的时间 |
| `double getDelta() const` | 获取最近两次 tick 之间的时间差 |
| `Clock& setFrame(uint64_t f, bool deferToNextTick = false)` | 设置当前帧 ID |
| `uint64_t getFrame() const` | 获取当前帧 ID |
| `double getRealTimeDelta() const` | 获取实际的实时时间差（不受时间缩放和帧率模拟影响） |
| `Clock& setExitTime(double seconds)` | 设置应用退出时间 |
| `Clock& setExitFrame(uint64_t frame)` | 设置应用退出帧 |
| `bool shouldExit() const` | 检查是否应该退出应用 |
| `bool setStartTime(double startTime)` | 设置时间循环的起始时间 |
| `bool setEndTime(double endTime)` | 设置时间循环的结束时间 |
| `Clock& tick()` | 推进时钟一帧，暂停时无效 |
| `Clock& setFramerate(uint32_t fps)` | 设置模拟帧率，0 表示禁用（使用实时模式） |
| `uint32_t getFramerate() const` | 获取当前模拟帧率 |
| `Clock& pause()` | 暂停时钟 |
| `Clock& play()` | 恢复时钟 |
| `Clock& stop()` | 停止时钟（暂停 + 重置为 0） |
| `Clock& step(int64_t frames = 1)` | 步进指定帧数，支持负数后退 |
| `Clock& setTimeScale(double scale)` | 设置时间缩放因子（实时模式下有效） |
| `double getTimeScale() const` | 获取时间缩放因子 |
| `bool isPaused() const` | 检查是否暂停 |
| `bool isSimulatingFps() const` | 检查是否处于帧率模拟模式 |
| `void renderUI(Gui::Window& w)` | 渲染时钟 GUI 控件 |
| `std::string getScript(const std::string& var) const` | 生成 Python 脚本字符串 |

#### 内部结构

| 结构体 | 说明 |
|--------|------|
| `Time` | 存储当前时间 `now` 和时间增量 `delta`，提供 `update` 方法 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mRealtime` | `Time` | 实时时间追踪 |
| `mTime` | `Time` | 逻辑时间追踪（受缩放和模拟帧率影响） |
| `mFramerate` | `uint32_t` | 模拟帧率，0 表示实时模式 |
| `mFrames` | `uint64_t` | 当前帧计数 |
| `mTicksPerFrame` | `uint64_t` | 每帧的内部 tick 数 |
| `mTimer` | `CpuTimer` | CPU 计时器 |
| `mPaused` | `bool` | 暂停状态 |
| `mScale` | `double` | 时间缩放因子，默认 1.0 |
| `mDeferredTime` | `std::optional<double>` | 延迟设置的时间值 |
| `mDeferredFrameID` | `std::optional<uint64_t>` | 延迟设置的帧 ID |
| `mExitTime` | `double` | 退出时间阈值 |
| `mExitFrame` | `uint64_t` | 退出帧阈值 |
| `mStartTime` | `double` | 时间循环起始 |
| `mEndTime` | `double` | 时间循环结束，-1 表示禁用 |

## 依赖关系

### 本文件引用

- `CpuTimer.h` — CPU 计时器
- `Core/Macros.h` — 宏定义
- `Utils/UI/Gui.h` — GUI 渲染
- `Utils/Logger.h` — 日志
- `Utils/Scripting/ScriptBindings.h` — Python 绑定
- `Utils/Scripting/ScriptWriter.h` — 脚本序列化

### 被以下文件引用

- `FrameRate.h` — `FrameRate` 类内部使用 `Clock` 计时
- 应用主循环通过 `Clock::tick()` 驱动时间前进

## 实现细节

- **高精度 Tick 系统**: 内部使用 `kTicksPerSecond = 14400 * (1 << 16)` 的 tick 精度（14400 是常见帧率的公倍数，乘以 65536 提供帧内细分精度），确保时间和帧数之间的转换精确。
- **时间循环**: `clampTime` 方法在时间超出 `[mStartTime, mEndTime)` 范围时自动将时间回绕到起点，实现无缝时间循环。
- **延迟应用**: `setTime` 和 `setFrame` 的 `deferToNextTick` 参数将变更缓存起来，在下一次 `tick()` 调用时统一生效，避免在帧中间改变时间状态。
- **GUI 渲染**: `renderUI` 提供播放/暂停/停止/倒退/单步前进等按钮，帧率模拟模式下额外显示帧 ID 编辑器和常用 FPS 下拉列表。
- **Python 绑定**: 通过 `FALCOR_SCRIPT_BINDING(Clock)` 导出 `time`、`frame`、`framerate`、`timeScale`、`exitTime`、`exitFrame` 等属性以及 `pause`、`play`、`stop`、`step` 等方法。
