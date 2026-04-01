# SDFSVOBuildOctree.cs.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SparseVoxelOctree/SDFSVOBuildOctree.cs.slang`
> 类型: 计算着色器 (.cs.slang)
> 模块: Scene/SDFs/SparseVoxelOctree

## 功能概述

计算着色器,从排序后的位置码和哈希表构建最终的八叉树数据结构。每个线程处理一个体素:从哈希表读取数据,查找第一个有效子节点的 SVO 偏移,编码关系数据(子节点偏移 + 有效掩码),写入最终 SVO 缓冲区。

## 资源绑定

| 资源 | 类型 | 说明 |
|------|------|------|
| `gVoxelHashTable` | `ParameterBlock<SDFSVOHashTable>` | 体素哈希表 |
| `gLocationCodes` | `RWByteAddressBuffer` | 排序后的位置码 |
| `gSVO` | `RWStructuredBuffer<SDFSVOVoxel>` | 输出 SVO 缓冲区 |

## 依赖关系

### import
- `Scene.SDFs.SDFVoxelCommon`, `Scene.SDFs.SDFVoxelTypes`
- `Scene.SDFs.SparseVoxelOctree.SDFSVOHashTable`

## 实现细节

- 线程组大小 64x1x1
- `relationData` 的低 8 位存储子节点有效掩码,高 24 位存储第一个有效子节点的 SVO 偏移
- 通过遍历有效掩码找到第一个有效子节点 ID,创建其位置码并从哈希表查询 SVO 偏移
