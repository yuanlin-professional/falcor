# SceneTypes.slang 源码文档

> 路径: `Source/Falcor/Scene/SceneTypes.slang`
> 类型: Slang 着色器文件（主机/设备共享）
> 模块: Scene

## 功能概述

SceneTypes.slang 定义了场景中所有核心数据类型，在 CPU 和 GPU 之间共享。包括几何实例 ID、几何类型枚举、实例数据、网格描述、顶点数据（静态/打包/蒙皮）、曲线数据、自定义图元以及分割缓冲区（SplitBuffer）的 GPU 表示。

这是场景模块中最基础的类型定义文件。

## 结构体与接口

### `GeometryInstanceID`

几何实例的唯一标识符，通过 `InstanceID() + GeometryIndex()` 计算得到。

| 成员 | 类型 | 说明 |
|------|------|------|
| `index` | `uint` | 全局实例索引 |

### `GeometryType` 枚举

| 值 | 说明 |
|---|------|
| `TriangleMesh` | 三角形网格 |
| `DisplacedTriangleMesh` | 位移三角形网格 |
| `Curve` | 曲线 |
| `SDFGrid` | SDF 网格 |
| `Custom` | 自定义图元 |

### `GeometryInstanceFlags` 枚举

| 标志 | 说明 |
|------|------|
| `Use16BitIndices` | 使用 16 位索引 |
| `IsDynamic` | 动态网格（蒙皮或顶点动画） |
| `TransformFlipped` | 变换翻转了坐标系手性 |
| `IsObjectFrontFaceCW` | 对象空间正面为顺时针 |
| `IsWorldFrontFaceCW` | 世界空间正面为顺时针 |

### `GeometryInstanceData`

| 成员 | 类型 | 说明 |
|------|------|------|
| `flags` | `uint` | 标志位（高位存储几何类型） |
| `globalMatrixID` | `uint` | 全局变换矩阵 ID |
| `materialID` | `uint` | 材质 ID |
| `geometryID` | `uint` | 几何体 ID |
| `vbOffset` | `uint` | 顶点缓冲区偏移 |
| `ibOffset` | `uint` | 索引缓冲区偏移 |
| `instanceIndex` | `uint` | TLAS 中的 InstanceIndex |
| `geometryIndex` | `uint` | BLAS 中的 GeometryIndex |

### `MeshDesc`

网格描述（32 字节）。

| 成员 | 类型 | 说明 |
|------|------|------|
| `vbOffset` | `uint` | 全局顶点缓冲区偏移 |
| `ibOffset` | `uint` | 全局索引缓冲区偏移 |
| `vertexCount` | `uint` | 顶点数 |
| `indexCount` | `uint` | 索引数 |
| `skinningVbOffset` | `uint` | 蒙皮数据偏移 |
| `prevVbOffset` | `uint` | 上一帧顶点数据偏移 |
| `materialID` | `uint` | 材质 ID |
| `flags` | `uint` | 网格标志（MeshFlags） |

### `StaticVertexData`

静态顶点数据。

| 成员 | 类型 | 说明 |
|------|------|------|
| `position` | `float3` | 位置 |
| `normal` | `float3` | 着色法线 |
| `tangent` | `float4` | 着色切线（w 分量为副切线符号） |
| `texCrd` | `float2` | 纹理坐标 |
| `curveRadius` | `float` | 曲线截面半径 |

### `PackedStaticVertexData`

打包的静态顶点数据（32 字节对齐），法线和切线使用半精度浮点编码。

### `SplitVertexBuffer` / `SplitIndexBuffer`

GPU 端分割缓冲区适配器，当缓冲区超过 4GB 时，使用索引高位选择子缓冲区。

### 其他类型

| 类型 | 说明 |
|------|------|
| `PrevVertexData` | 上一帧顶点位置 |
| `SkinningVertexData` | 蒙皮顶点数据（骨骼 ID、权重等） |
| `CurveDesc` | 曲线描述 |
| `StaticCurveVertexData` | 曲线顶点数据 |
| `DynamicCurveVertexData` | 动态曲线顶点数据 |
| `CustomPrimitiveDesc` | 自定义图元描述 |

## 依赖关系 / import

- `Utils/HostDeviceShared.slangh` - 主机/设备共享宏
- `SceneDefines.slangh` - 几何类型宏定义
- `Utils.Math.PackedFormats` - 打包格式工具（GPU 端）
- `Utils.SlangUtils` - Slang 工具
- `Utils.Attributes` - 着色器属性
- `Scene.VertexData`（导出，GPU 端） - 插值后的顶点数据
