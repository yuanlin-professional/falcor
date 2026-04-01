# SDFSBSPruneEmptyBricks.cs.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SparseBrickSet/SDFSBSPruneEmptyBricks.cs.slang`
> 类型: 计算着色器 (.cs.slang)
> 模块: Scene/SDFs/SparseBrickSet

## 功能概述

计算着色器,用于剪枝不包含隐式表面的空砖块。提供两级剪枝:粗剪枝通过评估砖块中心的距离值快速排除远离表面的砖块;细剪枝逐体素检测砖块内是否真正包含表面。

## 入口函数

| 函数签名 | 说明 |
|----------|------|
| `void coarsePrune(uint3)` | 粗剪枝:评估砖块中心距离值,距离过大则标记无效 |
| `void finePrune(uint3, uint3, uint)` | 细剪枝:逐体素评估,检测是否有任何体素包含表面 |

## 辅助函数

| 函数签名 | 说明 |
|----------|------|
| `float fetchGridDistance(float3)` | 从距离场纹理获取三线性插值距离值(仅合并模式) |
| `float evalCoords(uint3)` | 评估网格坐标处的距离值 |
| `float evalPos(float3)` | 评估任意位置的距离值(合并基元和距离场) |

## 条件编译宏

| 宏 | 说明 |
|----|------|
| `_BUILD_WITH_SD_FIELD` | 合并已有距离场 |
| `BRICK_WIDTH` | 砖块宽度 |

## 依赖关系

### import
- `Scene.SDFs.SDF3DPrimitive`, `Scene.SDFs.SDFVoxelCommon`, `Utils.Math.PackedFormats`

## 实现细节

- 粗剪枝:线程组 256x1x1,每线程一个砖块,中心距离值 <= 砖块宽度则保留
- 细剪枝:线程组为 `(brickWidth+1)^3`,使用组共享内存存储所有角点值
- 组管理线程遍历所有体素的有效性,任一有效则标记整个砖块为有效
