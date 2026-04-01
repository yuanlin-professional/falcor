# SDFSVO.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SparseVoxelOctree/SDFSVO.slang`
> 类型: Slang 着色器文件
> 模块: Scene/SDFs/SparseVoxelOctree

## 功能概述

稀疏体素八叉树的 GPU 端实现。实现 `ISDFGrid` 接口,使用基于栈的八叉树光线遍历算法(类似 "Efficient Sparse Voxel Octrees" 论文中的方法)。通过 PUSH/ADVANCE/POP 三种操作在八叉树中追踪光线,在叶节点附近使用球体追踪进行精确求解。

## 结构体与接口

### `StackData`

| 成员 | 类型 | 说明 |
|------|------|------|
| `relationData` | `uint` | 保存的关系数据(子节点偏移和有效掩码) |
| `tMax` | `float` | 保存的 tMax 值 |

### `SDFSVO`

- **继承**: `SDFGridBase`, `ISDFGrid`

#### 常量

| 常量 | 值 | 说明 |
|------|-----|------|
| `kStackSize` | 23 | 遍历栈大小(最大层级数) |
| `kEpsilon` | `1 / 2^23` | 方向分量最小值 |
| `kCoarsestLevelNormalizationFactor` | `0.5 * sqrt(3)` | 最粗级别归一化因子 |

#### 成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `svo` | `StructuredBuffer<SDFSVOVoxel>` | SVO 数据缓冲区 |
| `levelCount` | `uint` | 层级数 |

#### 函数

| 方法签名 | 说明 |
|----------|------|
| `float3 undoMirroring(float3 p, float scale, uint octMask)` | 撤销遍历算法中的坐标镜像 |
| `bool intersectSDF(...)` | 八叉树光线遍历 + 球体追踪交叉测试 |
| `bool intersectSDFAny(...)` | 仅判断相交 |
| `void decodeHit(...)` | 从 SVO 节点解码命中数据并计算梯度 |

## 依赖关系

### import
- `Scene.SDFs.SDFVoxelCommon`, `Scene.SDFs.SDFVoxelHitUtils`, `Scene.SDFs.SDFVoxelTypes`
- `Scene.SDFs.SDFGridBase`, `Utils.Geometry.IntersectionHelpers`, `Utils.Math.FormatConversion`

## 实现细节

- 光线原点偏移到 `[1, 2]` 空间以利用浮点位操作
- 使用八叉掩码(octMask)将光线方向统一转为负方向,简化遍历逻辑
- 三种遍历操作:
  - **PUSH**: 进入子节点,将当前节点压栈
  - **ADVANCE**: 进入同级邻居节点
  - **POP**: 回退到父节点(通过浮点位差异确定目标层级)
- 在接近叶节点(hierarchy < stackSize - levelCount + 2)时执行体素级球体追踪
- 梯度计算通过位置码解码体素坐标,使用不连续数值梯度方法
