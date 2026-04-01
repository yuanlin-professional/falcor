# GpuTimer.slang 源码文档

> 路径: `Source/Falcor/Utils/Timing/GpuTimer.slang`
> 类型: Slang 着色器文件
> 模块: Utils/Timing

## 功能概述

基于 NVAPI 的 GPU 端计时器，利用 NVIDIA GPU 的全局硬件计时器（Global Timer）实现着色器内的时间测量。仅使用全局计时器的低 32 位，适用于大多数短时测量场景，但需要注意溢出回绕问题。

## 结构体与接口

### `GpuTimer`

- **职责**: 在 GPU 着色器中进行时间测量

#### 类型定义

| 类型 | 说明 |
|------|------|
| `TimePoint` | `uint` 类型别名，表示时间点（全局计时器低 32 位） |

#### 成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `startTime` | `TimePoint` | 计时器最后一次启动时记录的时间点 |

#### 函数

| 函数签名 | 说明 |
|----------|------|
| `[mutating] void start()` | 启动计时器，记录当前时间点到 `startTime` |
| `uint getElapsed()` | 返回自上次 `start()` 以来的经过时间 |
| `static TimePoint getCurrentTimePoint()` | 获取当前 GPU 时间点。NVAPI 可用时通过 `NvGetSpecial(NV_SPECIALOP_GLOBAL_TIMER_LO)` 获取；不可用时返回 0 |
| `static uint calcDuration(TimePoint start, TimePoint end)` | 计算两个时间点之间的持续时间，通过无符号减法自动处理一次溢出回绕 |

## 依赖关系

### import

- `Utils/NVAPI.slangh` — 提供 NVAPI 宏和 `NvGetSpecial` 函数访问全局计时器

### 被以下文件引用

- 需要在着色器中进行 GPU 时间测量的渲染 Pass

## 实现细节

- **条件编译**: 通过 `FALCOR_NVAPI_AVAILABLE` 宏判断 NVAPI 是否可用。不可用时 `getCurrentTimePoint` 固定返回 0，计时功能实质失效。
- **溢出处理**: `calcDuration` 使用无符号整数减法 `end - start`，当发生一次 32 位溢出回绕时（end < start），无符号减法的数学性质会自动得到正确的持续时间值。但如果发生多次回绕则结果不正确。
- **精度**: 使用 `uint`（32 位无符号整数）存储时间点，精度取决于 GPU 全局计时器的时钟频率。
