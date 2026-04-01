# IPhaseFunction.slang 源码文档

> 路径: `Source/Falcor/Rendering/Volumes/IPhaseFunction.slang`
> 类型: Slang 着色器模块
> 模块: Rendering/Volumes

## 功能概述

本文件定义了相位函数（Phase Function）的底层接口 `IPhaseFunction`。相位函数描述了光在参与介质中散射时的方向分布，是体积渲染的核心组件。

约定：
- 入射方向（wi）和出射方向（wo）均指向远离散射点的方向
- 入射方向为已知量，出射方向为采样目标

## 结构体与接口

### `IPhaseFunction` 接口

使用 `[anyValueSize(36)]` 指定动态分发时的存储大小。

| 方法 | 说明 |
|------|------|
| `eval(wi, wo) -> float3` | 评估相位函数值 f_p(wi, wo) |
| `sample<S>(wi, out wo, out pdf, out weight, inout sg) -> bool` | 重要性采样出射方向 |
| `evalPdf(wi, wo) -> float` | 评估采样出射方向 wo 的 PDF（立体角度量） |

## 依赖关系 / import

- `Utils.Sampling.SampleGeneratorInterface`（`__exported import`）

## 实现细节

- 接口使用 `[anyValueSize(36)]` 允许存储最大 36 字节的实现数据，足以容纳 `DualHenyeyGreensteinPhaseFunction`（含 3 个 float3 成员）
- `sample()` 的 `weight` 输出为 `f_p(wi, wo) / pdf(wo)`，便于直接用于蒙特卡洛积分
- 泛型参数 `S : ISampleGenerator` 允许使用不同的随机数生成器实现
