# NRDBuffers.slang 源码文档

> 路径: `Source/Falcor/RenderPasses/Shared/Denoising/NRDBuffers.slang`
> 类型: Slang 着色器模块
> 模块: 渲染通道/共享/降噪 (RenderPasses/Shared/Denoising)

## 功能概述

本文件定义了 NRD（NVIDIA Real-time Denoisers）降噪器所需的 GPU 缓冲区集合结构体 `NRDBuffers`。该结构体包含路径追踪器输出的逐样本辐射度、命中距离、发射光、反射率等数据缓冲区，以及逐像素的主要命中信息和 Delta 反射/透射路径的附加数据纹理。通过编译期宏 `OUTPUT_NRD_DATA` 和 `OUTPUT_NRD_ADDITIONAL_DATA` 控制各缓冲区的有效性。

## 结构体与接口

### `NRDBuffers`

- **职责**: 封装 NRD 降噪器所需的全部 GPU 缓冲区资源

#### 关键成员 — 基础数据 (需 `kOutputNRDData == true`)

| 成员 | 类型 | 说明 |
|------|------|------|
| `sampleRadiance` | `RWStructuredBuffer<NRDRadiance>` | 逐样本 NRD 辐射度数据 |
| `samplePrimaryHitNEEOnDelta` | `RWStructuredBuffer<float4>` | Delta+漫反射材质路径上的主要命中 NEE |
| `sampleHitDist` | `RWStructuredBuffer<float>` | 逐样本命中距离 |
| `sampleEmission` | `RWStructuredBuffer<float4>` | 逐样本发射光数据 |
| `sampleReflectance` | `RWStructuredBuffer<float4>` | 逐样本反射率数据 |
| `primaryHitEmission` | `RWTexture2D<float4>` | 逐像素主要命中发射光 |
| `primaryHitDiffuseReflectance` | `RWTexture2D<float4>` | 逐像素主要命中漫反射率 |
| `primaryHitSpecularReflectance` | `RWTexture2D<float4>` | 逐像素主要命中镜面反射率 |

#### 关键成员 — 附加数据 (需 `kOutputNRDAdditionalData == true`)

| 成员 | 类型 | 说明 |
|------|------|------|
| `deltaReflectionReflectance` | `RWTexture2D<float4>` | Delta 反射路径反射率 |
| `deltaReflectionEmission` | `RWTexture2D<float4>` | Delta 反射路径发射光 |
| `deltaReflectionNormWRoughMaterialID` | `RWTexture2D<float4>` | Delta 反射路径的世界法线、粗糙度、材质 ID |
| `deltaReflectionPathLength` | `RWTexture2D<float>` | Delta 反射路径长度 |
| `deltaReflectionHitDist` | `RWTexture2D<float>` | Delta 反射命中距离 |
| `deltaTransmissionReflectance` | `RWTexture2D<float4>` | Delta 透射路径反射率 |
| `deltaTransmissionEmission` | `RWTexture2D<float4>` | Delta 透射路径发射光 |
| `deltaTransmissionNormWRoughMaterialID` | `RWTexture2D<float4>` | Delta 透射路径的世界法线、粗糙度、材质 ID |
| `deltaTransmissionPathLength` | `RWTexture2D<float>` | Delta 透射路径长度 |
| `deltaTransmissionPosW` | `RWTexture2D<float4>` | Delta 透射路径世界空间位置 |

## 编译期常量

| 常量 | 说明 |
|------|------|
| `kOutputNRDData` | 是否输出基础 NRD 数据（由宏 `OUTPUT_NRD_DATA` 控制） |
| `kOutputNRDAdditionalData` | 是否输出附加 NRD 数据（由宏 `OUTPUT_NRD_ADDITIONAL_DATA` 控制） |

## 依赖关系

### import

| 模块/文件 | 说明 |
|-----------|------|
| `NRDData` | NRD 数据类型定义（`NRDRadiance` 等） |
