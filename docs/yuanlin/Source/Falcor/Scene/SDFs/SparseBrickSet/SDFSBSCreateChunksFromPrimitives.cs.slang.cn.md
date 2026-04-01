# SDFSBSCreateChunksFromPrimitives.cs.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SparseBrickSet/SDFSBSCreateChunksFromPrimitives.cs.slang`
> 类型: 计算着色器 (.cs.slang)
> 模块: Scene/SDFs/SparseBrickSet

## 功能概述

计算着色器,使用区间算术从 SDF 基元创建空间块(chunks)。实现自顶向下的空间细分:先在根级别评估基元的区间距离,标记包含表面的块,然后逐级细分直到达到砖块级别。这是基元构建流程的核心空间加速步骤。

## 入口函数

| 函数签名 | 说明 |
|----------|------|
| `void rootEntryPoint(uint3, uint)` | 根级别入口:在初始网格上评估所有块 |
| `void subdivideEntryPoint(uint3, uint3, uint)` | 细分入口:将有效的父块细分为 4x4x4 子块 |

## 辅助函数

| 函数签名 | 说明 |
|----------|------|
| `void calculateSubChunkBounds(uint3, out float3, out float3)` | 计算子块的中心和半尺寸 |
| `bool evalSubChunk(uint3, float3, float3)` | 使用区间算术评估子块是否包含表面 |

## 条件编译宏

| 宏 | 说明 |
|----|------|
| `_BUILD_WITH_SD_FIELD` | 合并已有距离场的区间值 |
| `CHUNK_WIDTH`, `BRICK_WIDTH` | 块和砖块宽度 |

## 依赖关系

### import
- `Scene.SDFs.SDF3DPrimitive`, `Utils.Math.IntervalArithmetic`
- `Utils.Math.PackedFormats`, `Utils.SDF.SDFOperations`

## 实现细节

- 使用 `SDF3DPrimitive::evalInterval` 进行区间评估
- `ivlContainsZero` 检测区间是否包含零(即可能包含表面)
- 加入小的 epsilon 容限防止浮点精度问题导致表面丢失
- 使用组共享内存缓存每组结果,由线程 0 批量写入全局缓冲区
