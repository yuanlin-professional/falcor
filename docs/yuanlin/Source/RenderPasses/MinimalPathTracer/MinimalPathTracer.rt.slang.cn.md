# MinimalPathTracer.rt.slang 源码文档

> 路径: `Source/RenderPasses/MinimalPathTracer/MinimalPathTracer.rt.slang`
> 类型: 光线追踪着色器 (Slang)
> 模块: RenderPasses/MinimalPathTracer

## 功能概述

MinimalPathTracer 的光线追踪着色器实现。实现了一个简单的暴力路径追踪器，在每个命中点均匀采样解析光源（点光源、方向光），并追踪一条散射光线对半球进行采样。支持三角形网格、位移三角形网格、曲线和 SDF 网格几何类型。该着色器作为路径追踪验证基准，实现保持尽可能简单/朴素。

## 结构体与接口

### `ShadowRayData`

| 字段 | 类型 | 说明 |
|------|------|------|
| `visible` | `bool` | 阴影光线是否到达光源（未被遮挡） |

### `ScatterRayData`

最大 72 字节的散射光线载荷。

| 字段 | 类型 | 说明 |
|------|------|------|
| `radiance` | `float3` | 路径累积的出射辐射度 |
| `terminated` | `bool` | 路径是否终止 |
| `thp` | `float3` | 当前路径吞吐量，在每个路径顶点更新 |
| `pathLength` | `uint` | 路径段数（原点为 0，第一次间接命中为 1） |
| `origin` | `float3` | 下一段路径的起点 |
| `direction` | `float3` | 下一段路径的方向 |
| `sg` | `SampleGenerator` | 每条光线的采样生成器状态（最大 16B） |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `ShadingData loadShadingData(HitInfo, float3, float3)` | 根据命中信息加载顶点/材质属性并构建着色数据 |
| `float3 getPrimaryRayDir(uint2, uint2, Camera)` | 获取主光线方向，优先使用绑定的视图向量缓冲区 |
| `bool traceShadowRay(float3, float3, float)` | 追踪阴影光线，判断光源可见性 |
| `void traceScatterRay(inout ScatterRayData)` | 追踪散射光线 |
| `float3 evalDirectAnalytic(ShadingData, IMaterialInstance, inout SampleGenerator)` | 均匀随机采样解析光源，评估直接光照贡献 |
| `bool generateScatterRay(ShadingData, IMaterialInstance, bool, float3, inout ScatterRayData)` | 采样材质生成新的散射光线 |
| `void handleHit(HitInfo, inout ScatterRayData)` | 处理命中点：加载着色数据、采样直接光照、生成散射光线 |
| `float3 tracePath(uint2, uint2)` | 主路径追踪函数，从 V-buffer 加载主命中点并循环追踪路径 |
| `void rayGen()` | 光线生成着色器入口点 |
| `void scatterMiss(inout ScatterRayData)` | 散射光线未命中着色器，累加环境贴图贡献 |
| `void shadowMiss(inout ShadowRayData)` | 阴影光线未命中着色器，标记光源可见 |
| `void scatterTriangleMeshClosestHit(...)` | 三角形网格最近命中着色器 |
| `void scatterTriangleMeshAnyHit(...)` | 三角形网格任意命中着色器（Alpha 测试） |
| `void shadowTriangleMeshAnyHit(...)` | 阴影光线三角形网格任意命中着色器 |
| `void displacedTriangleMeshIntersection()` | 位移三角形网格交叉测试着色器 |
| `void curveIntersection()` | 曲线交叉测试着色器 |
| `void sdfGridIntersection()` | SDF 网格交叉测试着色器 |

## 依赖关系 / import

- `Scene/SceneDefines.slangh` — 场景宏定义
- `Utils/Math/MathConstants.slangh` — 数学常量
- `Scene.Raytracing` — 光线追踪场景接口
- `Scene.Intersection` — 几何交叉测试
- `Utils.Math.MathHelpers` — 数学辅助函数
- `Utils.Geometry.GeometryHelpers` — 几何辅助函数
- `Utils.Sampling.SampleGenerator` — 采样生成器
- `Rendering.Lights.LightHelpers` — 光源采样辅助函数

## 实现细节

- **宏定义控制**: 主机端通过 `MAX_BOUNCES`、`COMPUTE_DIRECT`、`USE_IMPORTANCE_SAMPLING`、`USE_ANALYTIC_LIGHTS`、`USE_EMISSIVE_LIGHTS`、`USE_ENV_LIGHT`、`USE_ENV_BACKGROUND` 等宏定义配置着色器行为。
- **路径追踪循环**: 使用 for 循环结构实现路径追踪，在每个顶点显式采样内置光源，散射光线命中/未命中着色器处理面积光源贡献。
- **光源采样**: 解析光源通过均匀随机选择进行采样（概率 = 1/lightCount），使用一条阴影光线进行可见性测试。
- **几何类型支持**: 通过 `SCENE_HAS_GEOMETRY_TYPE` 宏条件编译，支持四种几何类型。
