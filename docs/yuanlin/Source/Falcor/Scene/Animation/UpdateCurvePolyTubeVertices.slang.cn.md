# UpdateCurvePolyTubeVertices.slang 源码文档

> 路径: `Source/Falcor/Scene/Animation/UpdateCurvePolyTubeVertices.slang`
> 类型: Slang 着色器文件（计算着色器）
> 模块: Scene/Animation

## 功能概述

实现多边形管道（PolyTube）曲线的 GPU 顶点更新计算着色器。PolyTube 是将曲线细分为三角网格的方式，每个曲线顶点对应 4 个网格顶点。此着色器在关键帧之间插值曲线位置，然后计算切线方向，通过四元数旋转将网格顶点对齐到新的曲线位置和方向。

## 结构体与接口

### `CurvePerKeyframe`

| 成员 | 类型 | 说明 |
|------|------|------|
| `vertexData` | `StructuredBuffer<DynamicCurveVertexData>` | 该关键帧的曲线顶点数据 |

### `CurvePolyTubeVertexUpdater`

| 成员 | 类型 | 说明 |
|------|------|------|
| `keyframeIndices` | `uint2` | 两个关键帧索引 |
| `t` | `float` | 插值因子 |
| `copyPrev` | `bool` | 是否仅复制前帧数据 |
| `vertexCount` | `uint` | 曲线顶点总数 |
| `indexCount` | `uint` | 曲线索引总数 |
| `curvePerKeyframe[]` | `CurvePerKeyframe` | 所有关键帧的顶点数据 |
| `perMeshData` | `StructuredBuffer<PerMeshMetadata>` | 网格元数据 |
| `perCurveData` | `StructuredBuffer<PerCurveMetadata>` | 曲线元数据 |
| `curveStrandIndexData` | `ByteAddressBuffer` | 曲线链索引（标记链的首尾） |
| `sceneVertexData` | `RWSplitVertexBuffer` | 输出：场景顶点数据 |
| `prevVertexData` | `RWStructuredBuffer<PrevVertexData>` | 输出：前帧顶点 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `DynamicCurveVertexData interpolateCurveVertex(uint vertexID)` | 在两个关键帧之间线性插值曲线顶点 |
| `void updateVertex(uint threadID, uint curveID)` | 更新一个网格顶点 |
| `void main(uint3 dispatchThreadID)` | 入口函数（x = 线程ID, y = 曲线ID） |

## 依赖关系

### import

- `Scene.SceneTypes` — 场景类型
- `Utils.Math.Quaternion` — 四元数运算（`from_to_rotation`、`rotate_vector`）
- `SharedTypes` — InterpolationInfo、PerCurveMetadata、PerMeshMetadata

## 实现细节

- 线程组大小为 256x1x1
- 调度维度：x = 最大曲线顶点数 * 4，y = PolyTube 曲线数量
- 每个曲线顶点映射到 4 个网格顶点（`threadID / 4` 为曲线顶点索引，`threadID % 4` 为网格顶点偏移）
- 切线方向根据链索引确定：非末端顶点使用前向差分，末端顶点使用后向差分
- 法线通过 `from_to_rotation` 四元数旋转从旧切线方向对齐到新切线方向
- 网格顶点位置 = 曲线位置 + 半径 * 法线方向
- 对 NaN/Inf 结果进行安全检查，回退为曲线中心位置
- 链的最后一个顶点标记为 `0xFFFFFFFF`，用于检测链边界
