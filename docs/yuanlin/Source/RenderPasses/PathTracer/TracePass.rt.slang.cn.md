# TracePass.rt.slang 源码文档

> 路径: `Source/RenderPasses/PathTracer/TracePass.rt.slang`
> 类型: Slang 光线追踪着色器
> 模块: RenderPasses/PathTracer

## 功能概述

路径追踪器的主光线追踪着色器入口文件。实现了光线生成（Ray Generation）、最近命中（Closest Hit）、未命中（Miss）、任意命中（Any Hit）和相交（Intersection）着色器。包含两种调度器实现：`ReorderingScheduler`（使用 SER/HitObject API 进行线程重排）和 `Scheduler`（传统全屏光线追踪调度）。支持三角网格、位移三角网格、曲线和 SDF 网格四种几何类型。

## 结构体与接口

### `PathPayload`

| 成员 | 说明 |
|------|------|
| `packed[5]` | 紧凑打包的路径状态数据 |
| `hit` | 命中信息 |
| `guideData` | 降噪引导数据 |
| `interiorList` | 内部材质栈 |
| `sg` | 采样生成器状态 |

| 方法 | 说明 |
|------|------|
| `pack(PathState)` | 将路径状态打包为负载 |
| `unpack(PathPayload)` | 从负载解包路径状态 |

### `ReorderingScheduler`

使用 HitObject API 和 SER 重排序的调度器，在处理命中/未命中前将控制权返回调用者。

### `Scheduler`

传统全屏光线追踪调度器，使用 TraceRay 进行标准光线追踪。

### `VisibilityQuery`

实现 `IVisibilityQuery` 接口，用于阴影光线的可见性查询。

## 函数

| 函数（着色器入口） | 说明 |
|-------------------|------|
| `rayGen()` | 光线生成着色器，为每个像素运行调度器 |
| `scatterMiss(...)` | 散射光线未命中着色器 |
| `scatterTriangleAnyHit(...)` | 三角形任意命中（Alpha 测试） |
| `scatterTriangleClosestHit(...)` | 三角形最近命中 |
| `displacedTriangleMeshIntersection()` | 位移三角网格相交 |
| `curveIntersection()` | 曲线相交 |
| `sdfGridIntersection()` | SDF 网格相交 |

## 依赖关系 / import

- `Scene.Raytracing`, `Scene.RaytracingInline` — 场景光线追踪
- `Scene.Intersection` — 几何相交
- `Rendering.Utils.PixelStats` — 像素统计
- `PathTracer`, `PathTracerNRD` — 路径追踪核心逻辑和 NRD 扩展
- `Utils.NVAPI` — NVIDIA API 扩展
