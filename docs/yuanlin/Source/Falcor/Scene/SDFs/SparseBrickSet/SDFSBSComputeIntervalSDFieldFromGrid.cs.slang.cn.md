# SDFSBSComputeIntervalSDFieldFromGrid.cs.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SparseBrickSet/SDFSBSComputeIntervalSDFieldFromGrid.cs.slang`
> 类型: 计算着色器 (.cs.slang)
> 模块: Scene/SDFs/SparseBrickSet

## 功能概述

计算着色器,从 SDF 网格纹理计算区间距离场。为每个块收集其包含的所有体素的最小和最大距离值,生成层级化的区间距离场纹理。用于基元构建流程中的空间加速(快速排除不包含表面的区域)。

## 入口函数

| 函数签名 | 说明 |
|----------|------|
| `void rootGather(...)` | 根级别收集:从原始网格纹理收集每个砖块区域内的距离范围 |
| `void chunkGather(...)` | 块级别收集:从父级区间距离场聚合子块的距离范围 |

## 条件编译宏

| 宏 | 说明 |
|----|------|
| `GROUP_BRICK_CREATION` | 使用组共享内存聚合模式 |
| `GROUP_WIDTH`, `BRICK_WIDTH`, `CHUNK_WIDTH` | 尺寸参数 |

## 依赖关系

### import
- `Scene.SDFs.SDFVoxelCommon`, `Utils.Math.PackedFormats`, `Utils.Math.IntervalArithmetic`

## 实现细节

- `rootGather` 支持两种模式:组共享内存聚合(每个组处理一个砖块)或逐体素循环
- `chunkGather` 在父级区间纹理上进行块级聚合
- 使用 `ivlCombine` 合并区间(取最小/最大值)
