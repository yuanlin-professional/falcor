# UpdateMeshVertices.slang 源码文档

> 路径: `Source/Falcor/Scene/Animation/UpdateMeshVertices.slang`
> 类型: Slang 着色器文件（计算着色器）
> 模块: Scene/Animation

## 功能概述

实现网格顶点缓存动画的 GPU 更新计算着色器。在两个关键帧之间插值网格顶点的位置、法线和切线，同时保存前帧位置。每个网格可以有独立的关键帧序列和插值参数。

## 结构体与接口

### `MeshPerKeyframe`

| 成员 | 类型 | 说明 |
|------|------|------|
| `vertexData` | `StructuredBuffer<PackedStaticVertexData>` | 该关键帧的压缩顶点数据 |

### `MeshVertexUpdater`

| 成员 | 类型 | 说明 |
|------|------|------|
| `copyPrev` | `bool` | 是否仅复制前帧数据 |
| `perMeshInterp` | `StructuredBuffer<InterpolationInfo>` | 每网格的插值信息 |
| `perMeshData` | `StructuredBuffer<PerMeshMetadata>` | 每网格的元数据 |
| `meshPerKeyframe[]` | `MeshPerKeyframe` | 所有网格所有关键帧的顶点数据 |
| `sceneVertexData` | `RWSplitVertexBuffer` | 输出：场景顶点数据 |
| `prevVertexData` | `RWStructuredBuffer<PrevVertexData>` | 输出：前帧顶点 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `StaticVertexData interpolateVertex(StaticVertexData v0, StaticVertexData v1, float t)` | 插值两个顶点（位置/法线/切线） |
| `void updateVertex(uint meshVertexID, uint meshID)` | 更新一个网格顶点 |
| `void main(uint3 dispatchThreadID)` | 入口函数（x = 顶点ID, y = 网格ID） |

## 依赖关系

### import

- `Scene.SceneTypes` — 场景类型（PackedStaticVertexData、StaticVertexData）
- `SharedTypes` — InterpolationInfo、PerMeshMetadata

## 实现细节

- 线程组大小为 256x1x1
- 调度维度：x = 最大网格顶点数，y = 网格数量
- 超出当前网格顶点数的线程直接返回
- 位置使用 lerp 插值，法线先 lerp 后归一化，切线的 xyz 分量先 lerp 后归一化
- 纹理坐标不参与插值，保持原始值
- 每个网格通过 `keyframeBufferOffset` 索引到全局关键帧缓冲区数组
- `copyPrev` 模式仅将当前场景顶点位置写入前帧缓冲区
- 编译时需定义 `MESH_KEYFRAME_COUNT` 宏
