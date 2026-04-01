# NDSDFGrid.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/NormalizedDenseSDFGrid/NDSDFGrid.slang`
> 类型: Slang 着色器文件
> 模块: Scene/SDFs/NormalizedDenseSDFGrid

## 功能概述

归一化密集有符号距离场网格的 GPU 端实现。实现了 `ISDFGrid` 接口,提供多级 LOD 光线追踪和梯度计算。使用自适应 LOD 策略:根据采样距离值动态调整 LOD 级别,在远离表面时使用粗级别加速,接近表面时切换到细级别以提高精度。

## 结构体与接口

### `NDSDFGrid`

- **继承**: `SDFGridBase`, `ISDFGrid`
- **职责**: 在 GPU 上执行光线-SDF 交叉测试,使用多级纹理和自适应 LOD

#### 成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `textures[]` | `Texture3D<float>[]` | 各级 LOD 的 3D 纹理数组 |
| `sampler` | `SamplerState` | 线性过滤采样器 |
| `lodCount` | `uint` | LOD 级别总数 |
| `coarsestLODAsLevel` | `uint` | 最粗 LOD 对应的层级 |
| `coarsestLODGridWidth` | `uint` | 最粗 LOD 的网格宽度 |
| `coarsestLODNormalizationFactor` | `float` | 最粗 LOD 的归一化因子 |
| `narrowBandThickness` | `float` | 窄带厚度 |

#### 函数

| 方法签名 | 说明 |
|----------|------|
| `void loadCornerValues(uint3, uint, out float4, out float4)` | 从指定 LOD 纹理加载体素 8 个角点值 |
| `float hwSample(float3 p, uint lod)` | 使用硬件纹理采样(线性插值,速度快但精度低) |
| `float swSample(float3 p, uint lod)` | 使用软件三线性插值(精度高) |
| `int calculateDeltaFinerLOD(float d)` | 根据归一化距离计算应跳转的 LOD 级别数 |
| `bool intersectSDF(...)` | 自适应 LOD 光线-SDF 交叉测试 |
| `bool intersectSDFAny(...)` | 自适应 LOD 仅判断相交 |
| `void decodeHit(...)` | 解码命中点并计算梯度 |
| `float3 calculateGradient(float3, uint lod)` | 在指定 LOD 计算梯度 |
| `float3 computeContinuousTetrahedronGradient(...)` | 使用跨体素连续的四面体法计算梯度 |

## 依赖关系

### import
- `Scene.SDFs.SDFGridBase`, `Scene.SDFs.SDFVoxelCommon`, `Scene.SDFs.SDFVoxelHitUtils`
- `Utils.Geometry.IntersectionHelpers`

## 实现细节

- 光线原点偏移 0.5 到 `[0, 1]` 空间
- 自适应 LOD 策略:当 `|d| >= 1` 时降低 LOD(使用 HW 插值),当 `|d| < 1` 时提高 LOD(使用 SW 插值)
- LOD 跳转量由 `floor(log2(1/|d|)) + 1` 计算
- 在最细 LOD 且体素包含表面时,使用 `SDFVoxelHitUtils::intersectSDFVoxel` 进行精确体素级求解
- 梯度计算支持两种模式:不连续数值梯度和连续四面体梯度(由 `SCENE_SDF_GRADIENT_EVALUATION_METHOD` 控制)
- 最大步数为 512
