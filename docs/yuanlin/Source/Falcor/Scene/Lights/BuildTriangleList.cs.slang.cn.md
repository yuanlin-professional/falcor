# BuildTriangleList.cs.slang 源码文档

> 路径: `Source/Falcor/Scene/Lights/BuildTriangleList.cs.slang`
> 类型: Slang 计算着色器
> 模块: Scene/Lights

## 功能概述

计算着色器，用于构建自发光三角形列表。为每个网格光源的每个三角形获取顶点位置、纹理坐标、面法线和面积，并打包存储到全局三角形数据缓冲区中。每次对一个网格光源调度一次。

## 结构体与接口

### 资源绑定

| 资源 | 类型 | 说明 |
|------|------|------|
| `gLightIdx` | `uint` (cbuffer) | 当前网格光源索引 |
| `gMaterialID` | `uint` (cbuffer) | 网格光源的材质 ID |
| `gInstanceID` | `uint` (cbuffer) | 全局几何实例 ID |
| `gTriangleCount` | `uint` (cbuffer) | 当前网格的三角形数量 |
| `gTriangleOffset` | `uint` (cbuffer) | 全局三角形列表中的偏移 |
| `gTriangleData` | `RWStructuredBuffer<PackedEmissiveTriangle>` | 输出三角形数据 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void buildTriangleList(uint3 DTid)` | 主入口：获取顶点数据并打包存储 |

## 依赖关系 / import

- `Utils/Math/MathConstants.slangh` — 数学常量
- `Utils.Color.ColorHelpers` — 颜色工具
- `Scene.Scene` — 场景数据访问（顶点位置、纹理坐标、法线计算）

## 实现细节

- 线程组大小：256x1x1，每线程处理一个三角形。
- 通过 `gScene.getVertexPositionsW()` 获取世界空间顶点位置。
- 通过 `gScene.getVertexTexCoords()` 获取纹理坐标。
- 通过 `gScene.computeFaceNormalAndAreaW()` 计算面法线和面积。
