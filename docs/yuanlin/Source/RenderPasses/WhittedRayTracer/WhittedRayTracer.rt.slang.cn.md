# WhittedRayTracer.rt.slang 源码文档

> 路径: `Source/RenderPasses/WhittedRayTracer/WhittedRayTracer.rt.slang`
> 类型: 光线追踪着色器 (Slang)
> 模块: RenderPasses/WhittedRayTracer

## 功能概述

WhittedRayTracer 的核心光线追踪着色器。实现了经典的 Whitted 光线追踪算法，重点演示不同纹理 LOD 过滤方法的实现：Mip0（固定 LOD 0）、RayCones（光线锥）、RayDiffs（光线微分）。支持理想镜面反射和折射，使用菲涅尔方程处理材质交互。

## 结构体与接口

### `ShadowRayData`

| 字段 | 类型 | 说明 |
|------|------|------|
| `visible` | `bool` | 光源是否可见 |

### `ScatterRayData`

光线锥模式下 128 字节，光线微分模式下 164 字节。

| 字段 | 类型 | 说明 |
|------|------|------|
| `radiance` | `float3` | 路径累积辐射度 |
| `terminated` | `bool` | 路径是否终止 |
| `hasHitRefractiveMaterial` | `bool` | 路径是否命中过折射材质 |
| `thp` | `float3` | 当前路径吞吐量 |
| `pathLength` | `uint` | 路径段数 |
| `origin` | `float3` | 下一段路径起点 |
| `direction` | `float3` | 下一段路径方向 |
| `rayCone` | `RayCone` | 光线锥（2 float），用于纹理 LOD |
| `rayDiff` | `RayDiff` | 光线微分（12 float），用于纹理 LOD |
| `sg` | `SampleGenerator` | 采样生成器状态 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float roughnessToSpread(float)` | 将粗糙度转换为光线锥展开角度 |
| `float3 getFlippedShadingNormal(ShadingData)` | 获取双面材质翻转后的着色法线 |
| `ITextureSampler createTextureSampler(uint2)` | 创建纹理采样器（根据纹理梯度或固定 LOD） |
| `ShadingData loadShadingData(uint2, float3, float3)` | 从 G-buffer 加载着色数据 |
| `float computeEta(ShadingData)` | 计算相对折射率 |
| `bool traceShadowRay(float3, float3, float)` | 追踪阴影光线 |
| `void traceScatterRay(inout ScatterRayData)` | 追踪散射光线 |
| `float3 evalDirectAnalytic(ShadingData, IMaterialInstance, float3, inout SampleGenerator)` | 遍历所有解析光源，评估直接光照 |
| `void generateReflectionRay(...)` | 生成反射光线，使用菲涅尔项或简化吞吐量 |
| `void scatterMiss(inout ScatterRayData)` | 散射光线未命中，添加环境贴图贡献（含 LOD 计算） |
| `void scatterClosestHit(...)` | 最近命中着色器，处理三种纹理 LOD 模式下的完整命中逻辑 |
| `void rayGen()` | 光线生成入口，处理主命中点的完整 LOD 初始化和路径追踪 |

## 依赖关系 / import

- `Utils/Math/MathConstants.slangh` — 数学常量
- `Scene.Raytracing` — 光线追踪场景接口
- `Utils.Math.MathHelpers` — 数学辅助函数
- `Utils.Sampling.SampleGenerator` — 采样生成器
- `Rendering.Lights.LightHelpers` — 光源采样辅助
- `Rendering.Materials.TexLODHelpers` — 纹理 LOD 辅助函数
- `Rendering.Materials.TexLODTypes` — 纹理 LOD 类型定义
- `Rendering.Materials.Fresnel` — 菲涅尔函数
- `WhittedRayTracerTypes` — 光线足迹过滤模式枚举

## 实现细节

- **纹理 LOD 三模式**:
  - **Mip0**: 固定使用 mip level 0，最简单但缺乏过滤
  - **RayCones**: 使用光线锥跟踪纹理足迹，支持 Combo 和 Unified 两种变体
  - **RayDiffs**: 使用光线微分计算精确纹理梯度
- **光线锥传播**: 在命中点计算曲率、传播光线锥距离，可选基于粗糙度扩展展开角度
- **各向异性过滤**: RayCones 和 RayDiffs 模式均支持各向异性过滤选项，在折射后自动切换
- **折射处理**: 使用菲涅尔方程判断全内反射 (TIR)，在 RayDiff 模式下正确传播折射光线微分
- **表面散射可视化**: 支持可视化表面散射角，使用 RGB 编码正/负/零值
- **粗糙度阈值**: 仅当材质粗糙度 <= 0.19 时才追踪反射路径
- **光源评估**: 与 MinimalPathTracer 不同，此着色器遍历所有解析光源（而非随机选择一个）
