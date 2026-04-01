# LightUpdater.cs.slang 源码文档

> 路径: `Source/Falcor/Rendering/RTXDI/LightUpdater.cs.slang`
> 类型: Slang 计算着色器
> 模块: Rendering/RTXDI

## 功能概述

本文件实现了 RTXDI 光源信息和局部光源 PDF 纹理的更新计算着色器。它从 Falcor 场景数据中读取发光三角形、解析光源和环境光的信息，转换为 RTXDI 使用的 `PackedPolymorphicLight` 格式，并更新局部光源的通量 PDF 纹理。

## 结构体与接口

### `LightUpdater`

| 成员 | 类型 | 说明 |
|------|------|------|
| `lightInfo` | `RWStructuredBuffer<PackedPolymorphicLight>` | RTXDI 光源信息输出缓冲区 |
| `localLightPdf` | `RWTexture2D<float>` | 局部光源 PDF 纹理 |
| `analyticLightIDs` | `StructuredBuffer<uint>` | 解析光源 ID 映射表 |
| `threadCount` | `uint2` | 调度线程数 |
| `totalLightCount` | `uint` | 总光源数 |
| `firstLocalAnalyticLight` | `uint` | 第一个局部解析光源的索引 |
| `firstInfiniteAnalyticLight` | `uint` | 第一个无限远解析光源的索引 |
| `envLightIndex` | `uint` | 环境光索引 |
| `updateEmissiveLights` | `bool` | 是否更新发光体光源 |
| `updateEmissiveLightsFlux` | `bool` | 是否更新发光体通量 |
| `updateAnalyticLights` | `bool` | 是否更新解析光源 |
| `updateAnalyticLightsFlux` | `bool` | 是否更新解析光源通量 |

## 函数

| 函数 | 说明 |
|------|------|
| `execute(uint2 threadID)` | 根据线程 ID 计算光源索引，更新对应光源信息和 PDF |
| `main(uint2 threadID)` | 计算着色器入口点，[numthreads(16, 16, 1)] |

## 依赖关系 / import

- `Utils/Math/MathConstants.slangh`
- `rtxdi/RtxdiMath.hlsli`（`RTXDI_LinearIndexToZCurve` Z 曲线索引转换）
- `Scene.Scene`（访问场景光源集合）
- `Utils.Color.ColorHelpers`
- `PolymorphicLight`（多态光源打包/采样）

## 实现细节

- 光源按索引范围分三类处理：
  - `[0, firstLocalAnalyticLight)`: 发光三角形 — 创建 `EmissiveLight`，从 `LightCollection` 读取位置、边和平均辐射度
  - `[firstLocalAnalyticLight, envLightIndex)`: 解析光源 — 创建 `AnalyticLight`，仅存储场景光源 ID
  - `envLightIndex`: 环境光 — 创建 `EnvLight`
- PDF 纹理使用 Z 曲线索引（`RTXDI_LinearIndexToZCurve`）以提升 GPU 缓存命中率
- 通过标志位精细控制更新范围，仅在光源属性变化时更新相应数据
