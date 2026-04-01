# VBufferRT.rt.slang 源码文档

> 路径: `Source/RenderPasses/GBuffer/VBuffer/VBufferRT.rt.slang`
> 类型: Slang 光线追踪着色器
> 模块: RenderPasses/GBuffer/VBuffer

## 功能概述

使用传统 TraceRay API 的 V-buffer 光线追踪着色器。实现光线生成、未命中、最近命中、任意命中和相交着色器，支持三角网格、位移三角网格、曲线和 SDF 网格四种几何类型。

## 结构体与接口

### `RayData`

空负载结构体（仅包含占位 dummy 变量）。

## 函数（着色器入口）

| 函数 | 说明 |
|------|------|
| `rayGen()` | 光线生成着色器 |
| `miss(...)` | 未命中着色器 |
| `anyHit(...)` | Alpha 测试 |
| `closestHit(...)` | 三角形最近命中 |
| `displacedTriangleMeshIntersection()` / `...ClosestHit(...)` | 位移网格 |
| `curveIntersection()` / `curveClosestHit(...)` | 曲线 |
| `sdfGridIntersection()` / `sdfGridClosestHit(...)` | SDF 网格 |

## 依赖关系 / import

- `Scene.Raytracing`, `Scene.Intersection` — 光线追踪和相交
- `Scene.SDFs.SDFGridHitData` — SDF 网格命中数据
- `VBufferRT` — V-buffer 核心逻辑
