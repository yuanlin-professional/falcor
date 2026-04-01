# RTXDIApplicationBridge.slangh 源码文档

> 路径: `Source/Falcor/Rendering/RTXDI/RTXDIApplicationBridge.slangh`
> 类型: Slang 着色器头文件 (.slangh)
> 模块: Rendering/RTXDI

## 功能概述

本文件实现了 RTXDI SDK 所要求的应用层桥接接口（Application Bridge）。RTXDI SDK 的重采样函数（`ResamplingFunctions.hlsli`）通过一组 `RAB_*` 前缀的函数与具体应用交互，本文件定义了这些函数在 Falcor 中的具体实现。

桥接内容包括：
1. **类型定义**: 将 Falcor 类型映射为 RTXDI 类型（`RAB_Surface`、`RAB_LightInfo` 等）
2. **G-Buffer 访问**: 从表面数据缓冲区加载当前/上一帧的表面信息
3. **光源操作**: 加载、存储和翻译光源信息
4. **采样函数**: BRDF 采样、光源采样、可见性查询
5. **辅助函数**: 随机数生成、材质相似性判断

## 结构体与接口

### 类型别名

| RTXDI 类型 | Falcor 类型 | 说明 |
|-----------|-------------|------|
| `RAB_Surface` | `SurfaceData` | 表面数据 |
| `RAB_LightInfo` | `PolymorphicLight` | 多态光源信息 |
| `RAB_LightSample` | `PolymorphicLightSample` | 光源采样结果 |
| `RAB_RandomSamplerState` | `TinyUniformSampleGenerator` | 随机数生成器状态 |

## 函数

### 空类型构造

| 函数 | 说明 |
|------|------|
| `RAB_EmptySurface()` | 创建空表面 |
| `RAB_EmptyLightInfo()` | 创建空光源信息 |
| `RAB_EmptyLightSample()` | 创建空光源样本 |

### G-Buffer 访问

| 函数 | 说明 |
|------|------|
| `computeRayDirection(pixel, previousFrame)` | 计算针孔相机的射线方向（支持当前/上一帧） |
| `RAB_GetGBufferSurface(pixelPosition, previousFrame)` | 从 G-Buffer 加载表面数据 |
| `RAB_IsSurfaceValid(surface)` | 检查表面是否有效 |
| `RAB_GetSurfaceWorldPos(surface)` | 获取表面世界坐标 |
| `RAB_GetSurfaceNormal(surface)` | 获取表面法线 |
| `RAB_GetSurfaceLinearDepth(surface)` | 获取线性深度 |

### 光源操作

| 函数 | 说明 |
|------|------|
| `RAB_LoadLightInfo(index, previousFrame)` | 按索引加载光源信息 |
| `RAB_StoreCompactLightInfo(linearIndex, lightInfo)` | 存储紧凑光源信息到 RIS 缓冲区 |
| `RAB_LoadCompactLightInfo(linearIndex)` | 加载紧凑光源信息 |
| `RAB_TranslateLightIndex(lightIndex, currentToPrevious)` | 帧间光源索引转换（当前实现直接返回原索引） |

### 采样与评估

| 函数 | 说明 |
|------|------|
| `RAB_GetLightSampleTargetPdfForSurface(lightSample, surface)` | 计算目标函数 p-hat（完整 BRDF 响应），用于 ReSTIR 重采样权重 |
| `RAB_GetLightTargetPdfForVolume(light, center, radius)` | 体积光网格构建权重（当前返回 0，不支持 ReGIR） |
| `RAB_GetSurfaceBrdfSample(surface, rng, out dir)` | BRDF 重要性采样（漫反射余弦 + GGX 高光） |
| `RAB_GetSurfaceBrdfPdf(surface, dir)` | 计算 BRDF 采样 PDF |
| `RAB_SamplePolymorphicLight(lightInfo, surface, uv)` | 对多态光源执行采样 |
| `RAB_EvaluateEnvironmentMapSamplingPdf(L)` | 评估环境贴图方向采样 PDF |
| `RAB_EvaluateLocalLightSourcePdf(params, lightIndex)` | 评估局部光源的重要性采样 PDF |

### 可见性

| 函数 | 说明 |
|------|------|
| `RAB_GetConservativeVisibility(surface, lightSample)` | 追踪保守可见性射线（剔除非不透明几何体） |
| `RAB_GetTemporalConservativeVisibility(curSurface, prevSurface, lightSample)` | 时间保守可见性（使用当前帧 BVH 近似） |

### BRDF 射线追踪

| 函数 | 说明 |
|------|------|
| `RAB_TraceRayForLocalLight(origin, direction, tMin, tMax, out lightIndex, out randXY)` | 追踪 BRDF 射线寻找局部光源（发光三角形） |

### 辅助函数

| 函数 | 说明 |
|------|------|
| `RAB_GetNextRandom(rng)` | 获取下一个随机数 |
| `RAB_AreMaterialsSimilar(a, b)` | 材质相似性比较（当前始终返回 true） |
| `RAB_InitRandomSampler(index, passIndex)` | 初始化随机采样器 |
| `RAB_GetEnvironmentMapRandXYFromDir(worldDir)` | 将世界方向转换为环境贴图 UV |

## 依赖关系 / import

- `Utils.Sampling.TinyUniformSampleGenerator`
- `Rendering.Materials.IsotropicGGX`（GGX NDF 采样和评估）
- `Rendering.Materials.Fresnel`（Schlick 菲涅尔）
- `SurfaceData`（本地模块）

## 实现细节

- **目标函数 p-hat**: 混合漫反射（Lambertian）和高光（GGX）BRDF，使用修改后的 Fresnel 项 `F/specular` 以降低噪声
- **BRDF 采样**: 根据 `diffuseProb` 在漫反射余弦半球采样和 GGX NDF 采样间选择
- **可见性射线**: 使用 `SceneRayQuery<0>`（无 alpha 测试）和 `RAY_FLAG_CULL_NON_OPAQUE` 标志
- **切线空间构建**: 使用 `perp_stark()` 从法线构建正交基，适用于各向同性 BRDF
- **光源索引翻译**: 当前帧间共享同一份光源数据，因此索引直接传递
