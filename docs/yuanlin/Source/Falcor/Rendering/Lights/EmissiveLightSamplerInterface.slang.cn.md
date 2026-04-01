# EmissiveLightSamplerInterface.slang 源码文档

> 路径: `Source/Falcor/Rendering/Lights/EmissiveLightSamplerInterface.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Lights

## 功能概述

本着色器文件定义了自发光光源采样器的 GPU 端接口和相关数据结构。所有自发光采样器实现（Uniform、Power、LightBVH）均需实现 `IEmissiveLightSampler` 接口，使其可以互换使用。同时定义了光源采样结果（`TriangleLightSample`）和命中点描述（`TriangleLightHit`）两个核心数据结构。

## 结构体与接口

### `TriangleLightSample`

- **职责**: 描述发光三角形上的一个采样点及其相关数据

| 成员 | 类型 | 说明 |
|------|------|------|
| `triangleIndex` | `uint` | 采样三角形的索引 |
| `posW` | `float3` | 光源上采样点的世界坐标 |
| `normalW` | `float3` | 采样点的世界空间法线 |
| `dir` | `float3` | 从着色点到采样点的归一化方向 |
| `distance` | `float` | 着色点到采样点的距离 |
| `Le` | `float3` | 发射辐射度，背面或无效时为零 |
| `pdf` | `float` | 相对于着色点立体角的概率密度，范围 [0, inf]，0 表示无效 |
| `uv` | `float2` | 采样点在三角形上的重心坐标 |

### `TriangleLightHit`

- **职责**: 描述发光三角形上的命中点（用于 PDF 评估）

| 成员 | 类型 | 说明 |
|------|------|------|
| `triangleIndex` | `uint` | 三角形索引 |
| `posW` | `float3` | 命中点世界坐标 |
| `normalW` | `float3` | 三角形面法线（背面命中时已翻转） |

### `IEmissiveLightSampler` (接口)

- **职责**: 自发光光源采样器的统一接口

#### 函数

| 函数签名 | 说明 |
|----------|------|
| `bool sampleLight<S : ISampleGenerator>(const float3 posW, const float3 normalW, const bool upperHemisphere, inout S sg, out TriangleLightSample ls)` | 采样一个光源点。`upperHemisphere` 为 true 时仅考虑上半球 |
| `float evalTriangleSelectionPdf(const float3 posW, const float3 normalW, const bool upperHemisphere, const uint triangleIndex)` | 评估选择指定三角形的概率 |
| `float evalPdf(const float3 posW, const float3 normalW, const bool upperHemisphere, const TriangleLightHit hit)` | 在着色点评估给定命中点的概率密度 |

## 依赖关系

### import 引用

- `Utils.Sampling.SampleGeneratorInterface` — 样本生成器接口（导出）
