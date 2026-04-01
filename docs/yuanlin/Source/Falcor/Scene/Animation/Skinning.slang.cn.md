# Skinning.slang 源码文档

> 路径: `Source/Falcor/Scene/Animation/Skinning.slang`
> 类型: Slang 着色器文件（计算着色器）
> 模块: Scene/Animation

## 功能概述

实现 GPU 端的骨骼蒙皮计算着色器。每个线程处理一个动态顶点，通过混合骨骼矩阵变换顶点的位置、法线和切线。同时保存前帧顶点位置用于运动向量计算。

## 结构体与接口

### `SkinningData`

| 成员 | 类型 | 说明 |
|------|------|------|
| `initPrev` | `bool` | 是否将当前帧数据复制到前帧 |
| `staticData` | `SplitVertexBuffer` | 原始未变形的全局顶点缓冲区 |
| `skinningData` | `StructuredBuffer<SkinningVertexData>` | 骨骼 ID 和权重 |
| `skinnedVertices` | `RWSplitVertexBuffer` | 输出：变形后的顶点缓冲区 |
| `prevSkinnedVertices` | `RWStructuredBuffer<PrevVertexData>` | 输出：前帧顶点位置 |
| `boneMatrices` | `StructuredBuffer<float4x4>` | 骨骼变换矩阵 |
| `inverseTransposeBoneMatrices` | `StructuredBuffer<float4x4>` | 骨骼逆转置矩阵 |
| `worldMatrices` | `StructuredBuffer<float4x4>` | 世界变换矩阵 |
| `meshBindMatrices` | `StructuredBuffer<float4x4>` | 网格绑定矩阵 |
| `meshInvBindMatrices` | `StructuredBuffer<float4x4>` | 网格逆绑定矩阵 |
| `inverseTransposeWorldMatrices` | `StructuredBuffer<float4x4>` | 逆转置世界矩阵 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float4x4 getBlendedMatrix(uint vertexId)` | 计算混合后的蒙皮矩阵（4 骨骼权重） |
| `float4x4 getInverseTransposeBlendedMatrix(uint vertexId)` | 计算混合后的逆转置矩阵（法线变换） |
| `uint getStaticVertexID(uint vertexId)` | 获取静态顶点索引 |
| `StaticVertexData getStaticVertexData(uint vertexId)` | 获取原始顶点数据 |
| `void storeSkinnedVertexData(uint, StaticVertexData, PrevVertexData)` | 存储蒙皮结果 |
| `float3 getCurrentPosition(uint vertexId)` | 获取当前变形后位置 |
| `void main(uint3 dispatchThreadID)` | 入口函数，每线程处理一个顶点 |

## 依赖关系

### import

- `Scene.SceneTypes` — 场景类型（SkinningVertexData、StaticVertexData 等）

## 实现细节

- 线程组大小为 256x1x1
- 蒙皮流程：混合骨骼矩阵 -> 应用绑定变换 -> 转到世界空间 -> 逆世界回到骨架局部 -> 逆绑定回到网格局部
- 具体公式：`boneMat = InvBind * InvWorld * (sum(boneMatrix[i] * weight[i])) * Bind`
- 位置使用混合矩阵变换，法线使用逆转置混合矩阵变换
- 切线仅使用 3x3 子矩阵变换（不含平移）
- `initPrev` 为 true 时，前帧位置直接使用当前计算结果（用于首帧初始化）
