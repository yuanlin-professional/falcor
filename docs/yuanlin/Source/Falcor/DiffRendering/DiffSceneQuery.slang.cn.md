# DiffSceneQuery.slang 源码文档

> 路径: `Source/Falcor/DiffRendering/DiffSceneQuery.slang`
> 类型: Slang 着色器模块
> 模块: 可微渲染 (DiffRendering)

## 功能概述

本文件实现了可微场景查询系统，提供可微的光线追踪和交点计算功能。核心结构体 `SceneQueryAD` 封装了可微的光线-三角形求交逻辑，支持两种交点模式：附着到光线（AttachToRay）和附着到几何体（AttachToGeometry），并提供可微的相机射线方向计算和屏幕坐标投影。

## 结构体与接口

### `RayAD`

- **实现接口**: `IDifferentiable`
- **职责**: 可微光线表示

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `origin` | `float3` | 光线起点 |
| `direction` | `float3` | 光线方向 |
| `pixel` | `uint2` | 关联的像素坐标 |

### `IntersectionAD`

- **实现接口**: `IDifferentiable`
- **职责**: 可微光线-三角形交点信息

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `instanceID` | `GeometryInstanceID` | 命中的网格实例 ID |
| `triangleID` | `uint` | 命中的三角形 ID |
| `barycentrics` | `float3` | 重心坐标 |
| `posW` | `float3` | 世界空间交点位置 |
| `normalW` | `float3` | 世界空间法线 |
| `tangentW` | `float3` | 世界空间切线 |
| `hitT` | `float` | 光线到交点的距离 |

### `SceneQueryAD`

- **实现接口**: `IDifferentiable`
- **职责**: 可微场景查询的核心结构体，提供可微光线追踪和交点计算

#### 内嵌枚举

| 枚举 | 值 | 说明 |
|------|-----|------|
| `DiffIntersectionMode.AttachToRay` | 0 | 梯度附着到光线参数（光线起点和方向） |
| `DiffIntersectionMode.AttachToGeometry` | 1 | 梯度附着到几何体参数（顶点位置） |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `diffSceneIO` | `DiffSceneIO` | 可微场景 I/O 包装器 |
| `gradInfo` | `SceneGradientInfo` | 场景梯度信息 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `[Differentiable] bool traceRayInlineAD(RayAD, out IntersectionAD, DiffIntersectionMode)` | 可微内联光线追踪，返回是否命中及交点信息 |
| `bool traceVisibilityRay(Ray)` | 非可微的可见性测试光线追踪 |
| `[Differentiable] IntersectionAD computeIntersectionAD(HitInfo, RayAD, DiffIntersectionMode)` | 从非可微 HitInfo 计算可微交点 |
| `[Differentiable] void computeShadingFrame(GeometryInstanceID, uint, float3, out float3, out float3)` | 计算着色法线和切线（通过重心插值） |
| `[Differentiable] float3 loadVertexPosW(GeometryInstanceID, uint, float3, out float3)` | 使用固定重心坐标加载世界空间顶点位置 |
| `[Differentiable] float3 loadVertexPosW(GeometryInstanceID, uint, RayAD, out float, out float3, out float3)` | 使用可微光线计算顶点位置和重心坐标 |
| `[Differentiable] float3 loadCameraPosition()` | 加载相机位置 |
| `[Differentiable] float2 computeCameraRayScreenPos(float3, float2)` | 将射线方向投影到屏幕坐标 |
| `[Differentiable] float3 computeCameraRayDirection(float2, float2)` | 从屏幕坐标计算可微相机射线方向 |

## 依赖关系

### import

| 模块/文件 | 说明 |
|-----------|------|
| `Utils/Math/MathConstants.slangh` | 数学常量（如 `FLT_MAX`） |
| `Scene.RaytracingInline` | 内联光线追踪接口 |
| `Utils.Math.MathHelpers` | 数学辅助函数 |
| `Utils.Geometry.GeometryHelpers` | 几何辅助函数（重心坐标计算等） |
| `Utils.Sampling.SampleGenerator` | 采样生成器 |
| `Utils.Debug.PixelDebug` | 像素调试工具 |
| `Rendering.Lights.LightHelpers` | 光照辅助函数 |
| `DiffRendering.DiffSceneIO` | 可微场景 I/O 包装器 |
