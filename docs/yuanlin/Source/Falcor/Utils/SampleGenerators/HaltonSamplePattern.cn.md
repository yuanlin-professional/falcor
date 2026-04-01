# HaltonSamplePattern 源码文档

> 路径: `Source/Falcor/Utils/SampleGenerators/HaltonSamplePattern.h` + `Source/Falcor/Utils/SampleGenerators/HaltonSamplePattern.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/SampleGenerators

## 功能概述

实现了基于 **Halton 低差异序列** 的采样模式生成器。Halton 序列是一种准随机（quasi-random）序列，具有低差异性质，能够比纯随机采样更均匀地覆盖采样空间。使用基数 2 和 3 分别生成 x 和 y 维度的采样值。

## 类与接口

### `HaltonSamplePattern`

- **继承**: `CPUSampleGenerator`
- **职责**: 基于 Halton 序列生成二维低差异采样点

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<HaltonSamplePattern> create(uint32_t sampleCount = 0)` | 静态工厂方法。sampleCount = 0 表示不循环，持续生成新的采样点 |
| `HaltonSamplePattern(uint32_t sampleCount)` | 构造函数，设置采样数量和初始状态 |
| `uint32_t getSampleCount() const override` | 返回设定的采样数量 |
| `void reset(uint32_t startID = 0) override` | 重置当前采样索引为 0 |
| `float2 next() override` | 返回下一个 Halton 采样点，范围 [-0.5, 0.5) |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mCurSample` | `uint32_t` | 当前采样索引 |
| `mSampleCount` | `uint32_t` | 采样模式的周期。0 表示无限（不循环） |

## 依赖关系

### 本文件引用

- `CPUSampleGenerator.h` — 基类
- `Core/Macros.h` — Falcor 宏定义
- `Utils/Math/Vector.h` — 向量类型

### 被以下文件引用

- 需要高质量亚像素抖动的渲染通道

## 实现细节

- **Halton 序列算法**：通过在给定基数下反转数字顺序来生成低差异序列
  - x 维度使用基数 2（二进制反转）
  - y 维度使用基数 3
- **范围映射**：原始 Halton 值在 [0, 1)，通过 `frac(value + 0.5) - 0.5` 映射到 [-0.5, 0.5)，使序列中的第 0 个点（值为 0）映射到原点
- **循环控制**：当 `mSampleCount > 0` 时，采样索引在达到 `mSampleCount` 时回绕；当 `mSampleCount = 0` 时，索引持续递增
- Halton 序列的一个已知问题是在高维度或大量采样时可能出现线性相关性，但在二维低采样数场景下表现良好
