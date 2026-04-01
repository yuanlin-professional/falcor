# MeshIO.cs.slang 源码文档

> 路径: `Source/Falcor/Scene/MeshIO.cs.slang`
> 类型: Compute Shader（Slang 计算着色器）
> 模块: Scene

## 功能概述

MeshIO 是一个 GPU 计算着色器，提供网格数据在 GPU 缓冲区之间的读写操作。包含两大功能：

1. **MeshLoader** - 从场景的全局顶点/索引缓冲区中读取网格的顶点数据（位置、纹理坐标）和三角形索引
2. **MeshUpdater** - 将修改后的顶点数据（位置、法线、切线、纹理坐标）写回到场景的全局顶点缓冲区

这对于 CPU-GPU 之间的网格数据交换（如逆向渲染中的网格优化）至关重要。

## 结构体与接口

### `MeshLoader`

| 成员 | 类型 | 说明 |
|------|------|------|
| `vertexCount` | `uint` | 顶点数量 |
| `vbOffset` | `uint` | 顶点缓冲区偏移 |
| `triangleCount` | `uint` | 三角形数量 |
| `ibOffset` | `uint` | 索引缓冲区偏移 |
| `use16BitIndices` | `bool` | 是否使用 16 位索引 |
| `scene` | `ParameterBlock<Scene>` | 场景参数块 |
| `positions` | `RWStructuredBuffer<float3>` | 输出：顶点位置 |
| `texcrds` | `RWStructuredBuffer<float3>` | 输出：纹理坐标 |
| `triangleIndices` | `RWStructuredBuffer<uint3>` | 输出：三角形索引 |

### `MeshUpdater`

| 成员 | 类型 | 说明 |
|------|------|------|
| `vertexCount` | `uint` | 顶点数量 |
| `vbOffset` | `uint` | 顶点缓冲区偏移 |
| `positions/normals/tangents/texcrds` | `StructuredBuffer<float3>` | 输入：更新后的顶点属性 |
| `vertexData` | `RWSplitVertexBuffer` | 输出：打包的顶点数据 |

## 函数

| 入口点 | 说明 |
|--------|------|
| `[numthreads(256,1,1)] getMeshVerticesAndIndices` | 并行读取网格的顶点和索引数据 |
| `[numthreads(256,1,1)] setMeshVertices` | 并行写入更新后的顶点数据 |

## 依赖关系 / import

- `Scene.SceneTypes` - 场景类型定义
- `Scene.Scene` - 场景数据结构
