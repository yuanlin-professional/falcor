# LoadShadingData.slang 源码文档

> 路径: `Source/RenderPasses/PathTracer/LoadShadingData.slang`
> 类型: Slang 着色器
> 模块: RenderPasses/PathTracer

## 功能概述

提供从命中信息加载着色数据的辅助函数。支持多种几何类型（三角网格、位移三角网格、曲线、SDF 网格）的顶点数据加载，并能根据命中点准备完整的着色数据（ShadingData），同时提供主命中点的纹理坐标微分计算功能。

## 函数

| 函数 | 说明 |
|------|------|
| `computeDerivativesAtPrimaryTriangleHit(...)` | 计算主命中三角形处的纹理坐标微分（ddx/ddy），用于纹理 LOD |
| `loadVertexData(HitInfo, float3, float3, out uint)` | 根据命中信息加载顶点数据，支持四种几何类型，同时输出材质 ID |
| `getMaterialInstanceHints(HitInfo, bool)` | 确定创建材质实例时的提示标志（是否调整着色法线等） |
| `loadShadingData(HitInfo, float3, float3)` | 完整加载着色数据，包括顶点数据和材质准备 |

## 依赖关系 / import

- `Scene.Shading` — 场景着色数据
- `Scene.Material.ShadingUtils` — 着色工具
- `Rendering.Materials.TexLODHelpers` — 纹理 LOD 辅助
- `Utils.Math.MathHelpers` — 数学工具
- `Params` — 路径追踪参数
