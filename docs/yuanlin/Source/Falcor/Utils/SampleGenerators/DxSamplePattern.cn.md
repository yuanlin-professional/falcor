# DxSamplePattern 源码文档

> 路径: `Source/Falcor/Utils/SampleGenerators/DxSamplePattern.h` + `Source/Falcor/Utils/SampleGenerators/DxSamplePattern.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/SampleGenerators

## 功能概述

实现了 **Direct3D 8x MSAA/SSAA 采样模式** 生成器。提供固定的 8 个预定义采样位置，对应 DirectX 标准的 8 倍多重采样抗锯齿模式。目前仅支持 8 个采样点。

## 类与接口

### `DxSamplePattern`

- **继承**: `CPUSampleGenerator`
- **职责**: 提供 Direct3D 8x MSAA 标准采样模式

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<DxSamplePattern> create(uint32_t sampleCount = 8)` | 静态工厂方法，创建 DX 采样模式生成器。目前仅支持 sampleCount = 8 |
| `DxSamplePattern(uint32_t sampleCount)` | 构造函数。若 sampleCount != 8，输出警告并使用 8 |
| `uint32_t getSampleCount() const override` | 返回 8（固定值 `kSampleCount`） |
| `void reset(uint32_t startID = 0) override` | 重置当前采样索引为 0 |
| `float2 next() override` | 返回下一个采样点，循环遍历 8 个预定义位置 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mCurSample` | `uint32_t` | 当前采样索引 |
| `kSampleCount` | `static constexpr uint32_t` | 固定采样数量 = 8 |
| `kPattern[8]` | `static const float2[]` | 预定义的 8 个采样位置 |

## 依赖关系

### 本文件引用

- `CPUSampleGenerator.h` — 基类
- `Core/Macros.h` — Falcor 宏定义
- `Utils/Math/Vector.h` — 向量类型
- `Utils/Logger.h` — 日志输出

### 被以下文件引用

- 抗锯齿、时间超采样等需要 MSAA 采样模式的渲染模块

## 实现细节

- 8 个采样位置均以 1/16 像素为单位定义，范围在 [-7/16, 7/16] 之间：
  ```
  ( 1/16, -3/16), (-1/16,  3/16), ( 5/16,  1/16), (-3/16, -5/16),
  (-5/16,  5/16), (-7/16, -1/16), ( 3/16,  7/16), ( 7/16, -7/16)
  ```
- 这些位置对应 DirectX 硬件的标准 8x MSAA 采样模式
- `next()` 使用模运算循环遍历所有采样位置
- 当前不支持其他采样数量（代码中标记为 FIXME）
