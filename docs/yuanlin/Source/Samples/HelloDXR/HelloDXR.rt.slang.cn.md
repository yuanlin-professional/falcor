# HelloDXR.rt.slang 着色器文档

> 路径: `Source/Samples/HelloDXR/HelloDXR.rt.slang`
> 类型: Slang 光线追踪着色器
> 模块: Samples/HelloDXR

## 功能概述

HelloDXR 示例的光线追踪着色器,实现了完整的光线追踪管线,包括光线生成(Ray Generation)、最近命中(Closest Hit)、任意命中(Any Hit)和未命中(Miss)着色器。支持主光线渲染、阴影光线检测和一次反射,可选使用薄透镜模型实现景深效果。

## 结构体与接口

### `PrimaryRayData`

| 成员 | 类型 | 说明 |
|------|------|------|
| `color` | `float4` | 光线命中后计算的颜色 |
| `depth` | `uint` | 当前光线追踪深度(用于限制反射递归) |
| `hitT` | `float` | 光线命中距离(-1 表示未命中) |

### `ShadowRayData`

| 成员 | 类型 | 说明 |
|------|------|------|
| `hit` | `bool` | 阴影光线是否命中几何体 |

### 常量缓冲区 `PerFrameCB`

| 成员 | 类型 | 说明 |
|------|------|------|
| `invView` | `float4x4` | 逆视图矩阵 |
| `viewportDims` | `float2` | 视口尺寸 |
| `tanHalfFovY` | `float` | 垂直FOV半角的正切值 |
| `sampleIndex` | `uint` | 当前采样索引 |
| `useDOF` | `bool` | 是否使用景深 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void rayGen()` | 光线生成着色器,根据是否启用景深选择针孔或薄透镜相机模型发射主光线 |
| `void primaryClosestHit(inout PrimaryRayData, BuiltInTriangleIntersectionAttributes)` | 主光线最近命中着色器,计算着色数据、直接光照(含阴影测试)和反射 |
| `void primaryAnyHit(inout PrimaryRayData, BuiltInTriangleIntersectionAttributes)` | 主光线任意命中着色器,用于非透明几何体的 Alpha 测试 |
| `void primaryMiss(inout PrimaryRayData)` | 主光线未命中着色器,返回背景色 |
| `void shadowAnyHit(inout ShadowRayData, BuiltInTriangleIntersectionAttributes)` | 阴影光线任意命中着色器,标记命中 |
| `void shadowMiss(inout ShadowRayData)` | 阴影光线未命中着色器,标记未命中 |
| `bool traceShadowRay(RayDesc ray)` | 发射阴影光线,使用 `RAY_FLAG_ACCEPT_FIRST_HIT_AND_END_SEARCH` 优化 |
| `float3 getReflectionColor(...)` | 计算反射颜色,仅在深度为0时追踪一次反射光线(非PBR近似) |

## 全局资源

| 资源 | 类型 | 说明 |
|------|------|------|
| `gOutput` | `RWTexture2D<float4>` | 光线追踪输出纹理 |

## 实现细节

- **光线生成**: 根据 `useDOF` 标志选择 `computeRayPinhole`(针孔相机)或 `computeRayThinlens`(薄透镜相机)生成主光线。
- **主光线命中**: 获取顶点数据和材质,使用显式 LOD 0 的纹理采样器。遍历所有解析光源,对每个光源追踪阴影光线,未被遮挡时累加直接光照贡献。
- **反射**: 仅在深度0的主光线命中时追踪一次反射光线,使用简化的非 PBR 反射模型(距离平方衰减 + GGX alpha 粗糙度加权)。
- **阴影光线**: 使用 `RAY_FLAG_ACCEPT_FIRST_HIT_AND_END_SEARCH` 标志加速,只需判断是否被遮挡,不需要精确的命中信息。
- **Alpha 测试**: 主光线和阴影光线的 Any Hit 着色器分别处理非透明几何体。

## 依赖关系 / import

| 模块 | 说明 |
|------|------|
| `Scene.Raytracing` | 场景光线追踪支持(加速结构、顶点数据获取、材质系统) |
| `Utils.Sampling.TinyUniformSampleGenerator` | 轻量级均匀采样生成器 |
| `Rendering.Lights.LightHelpers` | 解析光源辅助函数 |
