# EmissivePowerSampler.slang 源码文档

> 路径: `Source/Falcor/Rendering/Lights/EmissivePowerSampler.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Lights

## 功能概述

本着色器文件实现了基于发射功率的自发光光源采样器的 GPU 端逻辑。使用主机端构建的 Alias Table 数据，以 O(1) 时间复杂度按辐射通量成比例地选择发光三角形，然后在选中的三角形上进行均匀面积采样。最终 PDF 为三角形选择概率与三角形面积采样概率的乘积。

## 结构体与接口

### `EmissivePower`

- **职责**: 存储从主机端绑定的功率采样数据

| 成员 | 类型 | 说明 |
|------|------|------|
| `invWeightsSum` | `float` | 权重总和的倒数 |
| `triangleAliasTable` | `Buffer<uint2>` | 压缩的 Alias Table 缓冲区 |

### `EmissivePowerSampler`

- **实现**: `IEmissiveLightSampler`
- **职责**: GPU 端按功率加权采样发光三角形

#### 函数

| 函数签名 | 说明 |
|----------|------|
| `bool sampleLight<S>(...)` | 使用 Alias Table 选择三角形，然后均匀采样三角形面积 |
| `float evalTriangleSelectionPdf(...)` | 返回 `flux * invWeightsSum`，即该三角形被选中的概率 |
| `float evalPdf(...)` | 返回三角形选择概率与面积采样 PDF 的乘积 |

## 实现细节

### `sampleLight`

1. 用第一个随机数均匀索引 Alias Table 条目
2. 解压 `uint2` 数据：16 位半精度阈值 + 2 个 24 位索引（selectAbove/selectBelow）
3. 用第二个随机数与阈值比较，选择对应索引
4. 计算三角形选择 PDF = `flux[triangleIndex] * invWeightsSum`
5. 在选中三角形上均匀采样，最终 PDF 为两阶段 PDF 的乘积

## 依赖关系

### import 引用

- `Utils/Math/MathConstants.slangh` — 数学常量
- `Scene.Scene` — 场景全局变量
- `Utils.Sampling.SampleGeneratorInterface` — 样本生成器接口
- `Rendering.Lights.EmissiveLightSamplerHelpers` — 三角形采样辅助函数
- `Rendering.Lights.EmissiveLightSamplerInterface` — 采样器接口定义
