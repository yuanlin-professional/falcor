# UpdateCurveAABBs.slang 源码文档

> 路径: `Source/Falcor/Scene/Animation/UpdateCurveAABBs.slang`
> 类型: Slang 着色器文件（计算着色器）
> 模块: Scene/Animation

## 功能概述

实现曲线段 AABB（轴对齐包围盒）的 GPU 更新计算着色器。在曲线顶点动画更新后，重新计算每个曲线段的包围盒，用于光线追踪加速结构中的程序化图元。

## 结构体与接口

### `CurveAABBUpdater`

| 成员 | 类型 | 说明 |
|------|------|------|
| `AABBCount` | `uint` | AABB 总数量 |
| `dimX` | `uint` | 调度 X 维度（用于 2D -> 1D 索引映射） |
| `curveIndexData` | `ByteAddressBuffer` | 曲线索引缓冲区 |
| `curveVertices` | `StructuredBuffer<StaticCurveVertexData>` | 曲线顶点数据 |
| `curveAABBs` | `RWStructuredBuffer<AABB>` | 输出：更新后的 AABB |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void updateAABB(uint3 dispatchThreadID)` | 更新一个曲线段的 AABB |
| `void main(uint3 dispatchThreadID)` | 入口函数 |

## 依赖关系

### import

- `Scene.SceneTypes` — 场景类型（StaticCurveVertexData）
- `Utils.Math.AABB` — AABB 结构

## 实现细节

- 线程组大小为 16x16x1
- 每个线程处理一个曲线段（由 2 个相邻顶点组成）
- AABB 通过包含每个端点的 `position +/- radius` 计算
- 使用 2D 调度索引映射为 1D 索引以支持超过 65535 个曲线段
