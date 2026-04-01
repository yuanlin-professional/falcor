# NRDHelpers.slang 源码文档

> 路径: `Source/RenderPasses/PathTracer/NRDHelpers.slang`
> 类型: Slang 着色器
> 模块: RenderPasses/PathTracer

## 功能概述

提供路径追踪器中 NRD（NVIDIA Real-time Denoisers）数据生成的辅助函数。负责在路径追踪的各个阶段设置 NRD 所需的主命中点发射、反射率、命中距离、辐射度等数据，并写入 Delta 反射和 Delta 透射的引导缓冲区。

## 函数

| 函数 | 说明 |
|------|------|
| `setNRDPrimaryHitEmission(...)` | 设置主命中点的发射数据 |
| `setNRDPrimaryHitReflectance(...)` | 设置主命中点的漫反射和镜面反射率（使用 GGX 近似） |
| `setNRDSampleHitDist(...)` | 设置每样本的命中距离（第二个顶点时） |
| `setNRDPrimaryHitSeparatedRadiance(...)` | 分离 Delta 反射主命中的 NEE 辐射度 |
| `setNRDSampleEmission(...)` | 设置每样本发射数据，沿 Delta 路径累积 |
| `setNRDSampleReflectance(...)` | 设置每样本反射率，支持漫反射/镜面/Delta 路径的解调 |
| `writeNRDDeltaReflectionGuideBuffers(...)` | 写入 Delta 反射引导缓冲区（反射率、发射、法线、粗糙度、路径长度等） |
| `writeNRDDeltaTransmissionGuideBuffers(...)` | 写入 Delta 透射引导缓冲区 |

## 依赖关系 / import

- `Rendering.Materials.IMaterialInstance` — 材质实例接口
- `Rendering.Materials.IsotropicGGX` — 各向同性 GGX 模型
- `RenderPasses.Shared.Denoising.NRDBuffers`, `NRDConstants`, `NRDHelpers` — NRD 共享模块
- `Scene.Scene`, `Scene.ShadingData` — 场景和着色数据
- `PathState` — 路径状态
