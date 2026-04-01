# PathTracerNRD.slang 源码文档

> 路径: `Source/RenderPasses/PathTracer/PathTracerNRD.slang`
> 类型: Slang 着色器
> 模块: RenderPasses/PathTracer

## 功能概述

通过 Slang `extension` 机制扩展 `PathTracer` 结构体，添加 NRD 引导路径追踪的变体。实现了 Delta 反射和 Delta 透射材质的专用命中处理函数，用于生成 NRD 降噪器所需的引导缓冲区数据。这些引导路径独立于主路径追踪，沿着 Delta 表面追踪以获取稳定的降噪引导信息。

## 函数

| 函数 | 说明 |
|------|------|
| `handleDeltaReflectionHit(inout PathState)` | 处理 Delta 反射材质命中，追踪反射引导路径并写入引导缓冲区。沿 Delta 反射表面追踪直到遇到非 Delta 叶或到达路径终点 |
| `handleDeltaTransmissionHit(inout PathState)` | 处理 Delta 透射材质命中，追踪透射引导路径。强制使用 Delta 透射瓣，遇到全内反射时允许回退到 Delta 反射 |

## 依赖关系 / import

- `RenderPasses.Shared.Denoising.NRDBuffers`, `NRDConstants`, `NRDHelpers` — NRD 共享降噪模块
- `LoadShadingData` — 着色数据加载
- `PathTracer` — 被扩展的主结构体
- `NRDHelpers` — 本模块 NRD 辅助函数
