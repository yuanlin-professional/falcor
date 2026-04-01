# EmissiveLightSampler.slang 源码文档

> 路径: `Source/Falcor/Rendering/Lights/EmissiveLightSampler.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Lights

## 功能概述

本着色器文件是自发光光源采样器的 GPU 端调度层。它根据主机端设置的宏定义 `_EMISSIVE_LIGHT_SAMPLER_TYPE` 在编译时选择具体的采样器实现，并将其 typedef 为统一的 `EmissiveLightSampler` 类型。这使得上层着色器代码无需关心具体使用的是哪种采样策略，所有采样器遵循相同的 `IEmissiveLightSampler` 接口。

## 结构体与接口

### 类型别名 `EmissiveLightSampler`

根据 `_EMISSIVE_LIGHT_SAMPLER_TYPE` 的值，`EmissiveLightSampler` 被 typedef 为以下类型之一：

| 宏值 | 类型 | 说明 |
|------|------|------|
| `EMISSIVE_LIGHT_SAMPLER_UNIFORM` (0) | `EmissiveUniformSampler` | 均匀采样器 |
| `EMISSIVE_LIGHT_SAMPLER_LIGHT_BVH` (1) | `LightBVHSampler` | BVH 采样器 |
| `EMISSIVE_LIGHT_SAMPLER_POWER` (2) | `EmissivePowerSampler` | 功率采样器 |
| `EMISSIVE_LIGHT_SAMPLER_NULL` (0xff) | `NullEmissiveSampler` | 空采样器（默认） |

### `NullEmissiveSampler`

- **实现**: `IEmissiveLightSampler`
- **职责**: 空采样器，所有方法返回零/false，用于采样器未启用时的占位

#### 函数

| 函数签名 | 说明 |
|----------|------|
| `bool sampleLight<S>(...)` | 始终返回 false |
| `float evalTriangleSelectionPdf(...)` | 始终返回 0 |
| `float evalPdf(...)` | 始终返回 0 |

## 依赖关系

### import 引用

- `EmissiveLightSamplerType.slangh` — 采样器类型宏定义
- `Rendering.Lights.EmissiveLightSamplerInterface` — 采样器接口（导出）
- `Utils.Sampling.SampleGeneratorInterface` — 样本生成器接口（导出）
- `Rendering.Lights.EmissiveUniformSampler` — 均匀采样器（条件引用）
- `Rendering.Lights.LightBVHSampler` — BVH 采样器（条件引用）
- `Rendering.Lights.EmissivePowerSampler` — 功率采样器（条件引用）
