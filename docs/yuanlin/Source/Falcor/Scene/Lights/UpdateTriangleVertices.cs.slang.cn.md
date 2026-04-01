# UpdateTriangleVertices.cs.slang 源码文档

> 路径: `Source/Falcor/Scene/Lights/UpdateTriangleVertices.cs.slang`
> 类型: Slang 计算着色器
> 模块: Scene/Lights

## 功能概述

计算着色器，用于更新自发光三角形的顶点位置。当场景中有几何体发生变换变化（如动画）时，重新计算所有自发光三角形的世界空间顶点位置、面法线和面积。单次调度处理所有三角形。

## 结构体与接口

### 资源绑定

| 资源 | 类型 | 说明 |
|------|------|------|
| `gTriangleCount` | `uint` (cbuffer) | 三角形总数 |
| `gMeshData` | `StructuredBuffer<MeshLightData>` | 网格光源数据 |
| `gTriangleData` | `RWStructuredBuffer<PackedEmissiveTriangle>` | 三角形几何数据（读写） |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void updateTriangleVertices(uint3 DTid)` | 主入口：更新每个三角形的顶点位置、法线和面积 |

## 依赖关系 / import

- `Utils/Math/MathConstants.slangh` — 数学常量
- `Scene.Scene` — 场景数据访问

## 实现细节

- 线程组大小：256x1x1。
- 从打包数据中解包三角形，更新世界空间位置和法线后重新打包。
- 通过 `lightIdx` 查找所属网格光源，计算局部三角形索引。
