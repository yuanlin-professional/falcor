# RTXDI.slang 源码文档

> 路径: `Source/Falcor/Rendering/RTXDI/RTXDI.slang`
> 类型: Slang 着色器模块
> 模块: Rendering/RTXDI

## 功能概述

本文件是 RTXDI 模块的 GPU 端核心实现，定义了 `RTXDI` 结构体及其扩展方法（extension），作为渲染器与 RTXDI SDK 之间的桥接接口。它负责：

1. 定义 RTXDI 运行时所需的全部参数和 GPU 资源
2. 提供公共 API：`setSurfaceData()`、`setInvalidSurfaceData()`、`getFinalSample()` 供外部渲染器调用
3. 提供私有 API：`generateCandidates()`、`testCandidateVisibility()`、`presampleLocalLights()`、空间/时间/时空重采样等内部操作
4. 配置 RTXDI SDK 的宏定义和资源绑定，并包含 SDK 的 `ResamplingFunctions.hlsli`

全局变量 `gRTXDI` 是 `ParameterBlock<RTXDI>` 类型，供所有 RTXDI 相关着色器共享访问。

## 结构体与接口

### `RTXDI`

RTXDI 的全局数据结构，包含运行时参数和 GPU 资源绑定。

| 成员 | 类型 | 说明 |
|------|------|------|
| `params` | `RTXDI_ResamplingRuntimeParameters` | RTXDI 运行时参数（每帧可变） |
| `frameIndex` | `uint` | 当前帧索引 |
| `frameDim` | `uint2` | 帧尺寸（像素） |
| `pixelCount` | `uint` | 总像素数 |
| `rayEpsilon` | `float` | 光线追踪自交避免偏移 |
| `currentSurfaceBufferIndex` | `uint` | 当前帧表面数据缓冲区索引 |
| `prevSurfaceBufferIndex` | `uint` | 上一帧表面数据缓冲区索引 |
| `prevCameraU/V/W` | `float3` | 上一帧相机坐标轴 |
| `prevCameraJitter` | `float2` | 上一帧相机抖动 |
| `storeCompactLightInfo` | `bool` | 是否存储紧凑光源信息 |
| `useEmissiveTextures` | `bool` | 最终着色时是否查找发光纹理 |
| `localLightCandidateCount` | `uint` | 局部光源候选数 |
| `infiniteLightCandidateCount` | `uint` | 无限远光源候选数 |
| `envLightCandidateCount` | `uint` | 环境光候选数 |
| `brdfCandidateCount` | `uint` | BRDF 候选数 |
| `brdfCutoff` | `float` | BRDF 射线截断值 |
| `maxHistoryLength` | `uint` | 最大时间重用历史长度 |
| `biasCorrectionMode` | `uint` | 偏差校正模式 |
| `lightInfo` | `StructuredBuffer<PackedPolymorphicLight>` | 光源信息缓冲区 |
| `risBuffer` | `RWBuffer<uint2>` | 预采样光源瓦片缓冲区 |
| `surfaceData` | `RWStructuredBuffer<PackedSurfaceData>` | 表面数据缓冲区 |
| `reservoirs` | `RWStructuredBuffer<RTXDI_PackedReservoir>` | 蓄水池缓冲区 |
| `motionVectors` | `Texture2D<float2>` | 运动向量纹理 |
| `localLightPdfTexture` | `Texture2D` | 局部光源 PDF 纹理 |
| `envLightLuminanceTexture` | `Texture2D` | 环境光亮度纹理 |
| `envLightPdfTexture` | `Texture2D` | 环境光 PDF 纹理 |

## 函数

### 公共 API（RTXDI extension）

| 函数 | 说明 |
|------|------|
| `setSurfaceData(pixel, position, normal, diffuse, specular, roughness)` | 设置指定像素的主命中点表面数据 |
| `setInvalidSurfaceData(pixel)` | 标记像素无有效表面（如天空） |
| `getFinalSample(pixel, out dir, out distance, out Li) -> bool` | 获取最终光源样本方向、距离和加权入射辐射度 |

### 私有 API（仅在 RTXDI_INSTALLED 时定义）

| 函数 | 说明 |
|------|------|
| `generateCandidates(pixelID, outputReservoirIndex)` | 生成初始光源候选样本，选择一个存入蓄水池 |
| `testCandidateVisibility(pixelID, reservoirIndex)` | 对候选光源追踪可见性射线 |
| `presampleLocalLights(threadId)` | 将局部光源预采样到光源瓦片中 |
| `presampleEnvLight(threadId)` | 将环境光预采样到光源瓦片中 |
| `doSpatialResampling(pixelID, inIdx, outIdx)` | 执行空间重采样 |
| `doTemporalResampling(pixelID, groupId, candidateIdx, priorIdx, outIdx)` | 执行时间重采样（含沸腾滤波器） |
| `doSpatiotemporalResampling(pixelID, groupId, candidateIdx, priorIdx, outIdx)` | 执行时空联合重采样（含沸腾滤波器） |
| `getFinalSample(pixel, out surface, out lightSample, out Li) -> bool` | 获取最终样本（暴露 RTXDI 内部结构） |

## 依赖关系 / import

- `Utils/Math/MathConstants.slangh`
- `Scene.Scene`
- `Utils.Color.ColorHelpers`
- `Utils.Debug.PixelDebug`
- `PackedTypes`（本地模块）
- `Scene.RaytracingInline`
- `PolymorphicLight.slang`（通过 `#include` 内联，启用 `RTXDI_POLYMORPHIC_LIGHT_SAMPLING`）
- `RTXDIApplicationBridge.slangh`（通过 `#include`）
- `rtxdi/ResamplingFunctions.hlsli`（RTXDI SDK 重采样核心函数）

## 实现细节

- 线程组大小固定为 16x16（`kRTXDIGroupSize = 16`），受 RTXDI SDK 内部约束
- `setSurfaceData()` 计算漫反射/高光权重比，用于后续重采样的 BRDF 评估
- `getFinalSample()` 从蓄水池加载最终样本，乘以蓄水池逆 PDF 得到加权入射辐射度
- 沸腾滤波器（Boiling Filter）在时间和时空重采样中启用，抑制高方差样本的闪烁
- 当 RTXDI SDK 未安装时，`RTXDI` 结构体退化为空结构，公共 API 返回默认值
