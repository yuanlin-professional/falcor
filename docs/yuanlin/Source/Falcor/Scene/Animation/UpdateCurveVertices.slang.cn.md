# UpdateCurveVertices.slang 源码文档

> 路径: `Source/Falcor/Scene/Animation/UpdateCurveVertices.slang`
> 类型: Slang 着色器文件（计算着色器）
> 模块: Scene/Animation

## 功能概述

实现线性扫掠球体（LSS）曲线的 GPU 顶点更新计算着色器。在两个关键帧之间线性插值曲线顶点位置，同时保存前帧位置用于运动向量计算。

## 结构体与接口

### `CurvePerKeyframe`

| 成员 | 类型 | 说明 |
|------|------|------|
| `vertexData` | `StructuredBuffer<DynamicCurveVertexData>` | 该关键帧的曲线顶点数据 |

### `CurveVertexUpdater`

| 成员 | 类型 | 说明 |
|------|------|------|
| `keyframeIndices` | `uint2` | 两个关键帧索引 |
| `t` | `float` | 插值因子 |
| `copyPrev` | `bool` | 是否仅复制前帧数据 |
| `dimX` | `uint` | 调度 X 维度 |
| `vertexCount` | `uint` | 顶点总数 |
| `curvePerKeyframe[]` | `CurvePerKeyframe` | 所有关键帧数据 |
| `prevCurveVertices` | `RWStructuredBuffer<DynamicCurveVertexData>` | 输出：前帧顶点 |
| `curveVertices` | `RWStructuredBuffer<StaticCurveVertexData>` | 输出：当前帧顶点 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `DynamicCurveVertexData interpolateCurveVertex(uint vertexID)` | 线性插值曲线顶点位置 |
| `void updateVertex(uint3 dispatchThreadID)` | 更新一个顶点 |
| `void main(uint3 dispatchThreadID)` | 入口函数 |

## 依赖关系

### import

- `Scene.SceneTypes` — 场景类型（DynamicCurveVertexData、StaticCurveVertexData）

## 实现细节

- 线程组大小为 16x16x1
- 使用 2D 调度映射为 1D 顶点索引（`y * dimX + x`）
- `copyPrev` 模式：直接将当前位置复制到前帧缓冲区（不做插值）
- 正常模式：保存旧位置到前帧 -> 写入插值后的新位置
- 编译时需定义 `CURVE_KEYFRAME_COUNT` 宏
