# GBufferRT.rt.slang 源码文档

> 路径: `Source/RenderPasses/GBuffer/GBuffer/GBufferRT.rt.slang`
> 类型: Slang 光线追踪着色器
> 模块: RenderPasses/GBuffer

## 功能概述

使用传统 TraceRay API 的 G-buffer 光线追踪着色器。实现了光线生成、未命中、最近命中、任意命中和相交着色器入口点，支持三角网格、位移三角网格、曲线和 SDF 网格四种几何类型。

## 结构体与接口

### `RayData`

空负载结构体（G-buffer 模式不需要通过负载传递数据）。

## 函数（着色器入口）

| 函数 | 说明 |
|------|------|
| `rayGen()` | 光线生成着色器，生成主相机光线并调用 TraceRay |
| `miss(...)` | 未命中着色器，写入默认值 |
| `anyHit(...)` | 任意命中着色器（Alpha 测试） |
| `closestHit(...)` | 三角形最近命中，写入 G-buffer 数据 |
| `displacedTriangleMeshIntersection()` | 位移三角网格相交测试 |
| `displacedTriangleMeshClosestHit(...)` | 位移三角网格最近命中 |
| `curveIntersection()` / `curveClosestHit(...)` | 曲线相交和最近命中 |
| `sdfGridIntersection()` / `sdfGridClosestHit(...)` | SDF 网格相交和最近命中 |

## 依赖关系 / import

- `Scene.Raytracing` — 光线追踪场景接口
- `Scene.Intersection` — 几何相交测试
- `GBufferRT` — G-buffer 核心逻辑
