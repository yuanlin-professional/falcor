# LightCollection.slang 源码文档

> 路径: `Source/Falcor/Scene/Lights/LightCollection.slang`
> 类型: Slang 着色器文件
> 模块: Scene/Lights

## 功能概述

GPU 端的自发光几何数据集合结构体。提供对所有自发光三角形的访问接口，包括三角形数据查询、活跃三角形映射、通量/辐照度获取以及从实例 ID 和图元索引到三角形索引的查找。

## 结构体与接口

### `LightCollection`

| 成员 | 类型 | 说明 |
|------|------|------|
| `triangleCount` | `uint` | 自发光三角形总数 |
| `activeTriangleCount` | `uint` | 活跃（未剔除）三角形数 |
| `meshCount` | `uint` | 网格光源数 |
| `triangleData` | `StructuredBuffer<PackedEmissiveTriangle>` | 三角形几何数据（根缓冲区） |
| `activeTriangles` | `StructuredBuffer<uint>` | 活跃三角形索引列表 |
| `triToActiveMapping` | `StructuredBuffer<uint>` | 三角形到活跃索引的映射 |
| `fluxData` | `StructuredBuffer<EmissiveFlux>` | 三角形通量数据 |
| `meshData` | `StructuredBuffer<MeshLightData>` | 网格光源数据 |
| `perMeshInstanceOffset` | `StructuredBuffer<uint>` | 实例到三角形偏移的查找表 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `uint getTriangleCount()` | 获取自发光三角形总数 |
| `uint getActiveTriangleCount()` | 获取活跃三角形数 |
| `bool isEmpty()` | 检查集合是否为空 |
| `EmissiveTriangle getTriangle(uint triIdx)` | 获取三角形数据（解包） |
| `float3 getAverageRadiance(uint triIdx)` | 获取三角形平均辐照度 |
| `uint getTriangleIndex(GeometryInstanceID, uint primitiveIndex)` | 从实例 ID 和图元索引获取三角形索引 |
| `uint getActiveTriangleIndex(uint triIdx)` | 获取活跃三角形索引 |
| `uint getLightIdx(uint triIdx)` | 获取网格光源索引 |
| `MeshLightData getMeshData(uint triIdx)` | 获取网格光源数据 |

## 依赖关系 / import

- `Utils.Attributes` — 着色器属性工具
- `Scene.SceneTypes` — 场景类型定义
- `Scene.Lights.LightCollectionShared` — 共享数据结构（导出）
- `Scene.Lights.MeshLightData` — 网格光源数据（导出）
