# Raytracing.slang 源码文档

> 路径: `Source/Falcor/Scene/Raytracing.slang`
> 类型: Slang 着色器文件
> 模块: Scene

## 功能概述

Raytracing.slang 提供 DXR 1.0 光线追踪管线的辅助工具函数。它封装了 `TraceRay()` 调用，自动绑定场景的加速结构和光线类型计数。同时提供在 Hit Group 着色器中获取顶点属性的便捷方法。

## 结构体与接口

无自定义结构体。

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void TraceRayScene<payload_t>(uint rayFlags, uint instanceInclusionMask, uint rayType, uint missIndex, RayDesc ray, inout payload_t payload)` | 封装 `TraceRay()`，自动传入场景加速结构和 `rayTypeCount` |
| `GeometryInstanceID getGeometryInstanceID()` | 在 Hit Group 着色器中通过 `InstanceID() + GeometryIndex()` 获取全局几何实例 ID |
| `VertexData getVertexData(GeometryInstanceID, uint triangleIndex, BuiltInTriangleIntersectionAttributes)` | 获取命中三角形的插值顶点属性 |
| `VertexData getVertexData(GeometryInstanceID, uint triangleIndex, BuiltInTriangleIntersectionAttributes, out float3 barycentrics, out StaticVertexData vertices[3])` | 获取插值顶点属性并输出原始顶点数据 |
| `VertexData getVertexDataRayCones(GeometryInstanceID, uint triangleIndex, BuiltInTriangleIntersectionAttributes)` | 获取顶点属性（含光线锥纹理 LOD 信息） |
| `float3 getPrevPosW(GeometryInstanceID, uint triangleIndex, BuiltInTriangleIntersectionAttributes)` | 获取上一帧的世界空间插值位置（用于运动模糊） |
| `uint getRayTypeCount()` | 返回光线类型数量 |

## 依赖关系 / import

- `Utils.Attributes` - 着色器属性
- `Rendering.Materials.TexLODHelpers` - 纹理 LOD 辅助工具
- `Scene.Shading`（导出） - 着色相关模块
