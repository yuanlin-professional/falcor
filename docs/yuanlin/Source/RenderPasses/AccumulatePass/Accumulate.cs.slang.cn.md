# Accumulate.cs.slang 源码文档

> 路径: `Source/RenderPasses/AccumulatePass/Accumulate.cs.slang`
> 类型: 计算着色器 (Compute Shader)
> 模块: RenderPasses/AccumulatePass

## 功能概述

该计算着色器实现了时序累积的三种精度模式：单精度标准求和、单精度补偿求和（Kahan 求和）和双精度标准求和。每种模式有独立的入口点。所有模式都将当前累积平均值写入输出纹理，中间缓冲区由通道内部管理。

注意：补偿求和模式的着色器必须使用精确浮点模式编译（禁止运算重排序）。

## 结构体与接口

### 常量缓冲区

| 成员 | 类型 | 说明 |
|------|------|------|
| `gResolution` | `uint2` | 帧分辨率 |
| `gAccumCount` | `uint` | 当前已累积帧数 |
| `gAccumulate` | `bool` | 是否启用累积 |
| `gMovingAverageMode` | `bool` | 是否使用指数移动平均模式 |

### 宏定义

| 宏 | 值 | 说明 |
|------|------|------|
| `INPUT_FORMAT_FLOAT` | 0 | 浮点输入格式 |
| `INPUT_FORMAT_UINT` | 1 | 无符号整数输入格式 |
| `INPUT_FORMAT_SINT` | 2 | 有符号整数输入格式 |

### 资源绑定

| 名称 | 类型 | 说明 |
|------|------|------|
| `gCurFrame` | `Texture2D<InputDataType>` | 当前帧输入数据 |
| `gOutputFrame` | `RWTexture2D<float4>` | 累积平均输出 |
| `gLastFrameSum` | `RWTexture2D<float4>` | 上一帧累积和（Single/SingleCompensated 模式） |
| `gLastFrameCorr` | `RWTexture2D<float4>` | 上一帧补偿项（SingleCompensated 模式） |
| `gLastFrameSumLo` | `RWTexture2D<uint4>` | 上一帧累积和低 32 位（Double 模式） |
| `gLastFrameSumHi` | `RWTexture2D<uint4>` | 上一帧累积和高 32 位（Double 模式） |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void accumulateSingle(uint3 dispatchThreadId)` | 单精度标准求和入口，线程组 [16, 16, 1] |
| `void accumulateSingleCompensated(uint3 dispatchThreadId)` | 单精度补偿求和入口，线程组 [16, 16, 1] |
| `void accumulateDouble(uint3 dispatchThreadId)` | 双精度标准求和入口，线程组 [16, 16, 1] |

## 依赖关系/import

无外部依赖。

## 实现细节

### 单精度标准求和 (`accumulateSingle`)

- **无帧限制模式**: 直接维护累积和 `sum += curColor`，输出 `sum / (count + 1)`
- **移动平均模式**: 使用 `lerp(history, curColor, 1/(count+1))` 进行指数加权移动平均

### 单精度补偿求和 (`accumulateSingleCompensated`)

- 实现 Kahan 求和算法，维护补偿项 `c` 以跟踪浮点舍入误差
- 计算步骤: `y = curColor - c` -> `sumNext = sum + y` -> `c = (sumNext - sum) - y`
- 输出 `sumNext / (count + 1)`
- 不支持移动平均模式和帧数限制

### 双精度标准求和 (`accumulateDouble`)

- 使用 `double` 类型进行累积，通过两个 `uint4` 纹理分别存储每个 double 值的低 32 位和高 32 位
- 使用 `asdouble(lo, hi)` 和 `asuint(val, lo, hi)` 进行 double <-> uint 转换
- 支持无帧限制模式和移动平均模式

### 通用行为

- 当累积被禁用时，直接输出当前帧颜色
- 所有模式的线程组大小均为 [16, 16, 1]
- 权重公式 `curWeight = 1.0 / (gAccumCount + 1)` 确保等权平均
