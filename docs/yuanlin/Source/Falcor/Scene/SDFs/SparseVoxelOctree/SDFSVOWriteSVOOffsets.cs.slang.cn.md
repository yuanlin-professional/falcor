# SDFSVOWriteSVOOffsets.cs.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SparseVoxelOctree/SDFSVOWriteSVOOffsets.cs.slang`
> 类型: 计算着色器 (.cs.slang)
> 模块: Scene/SDFs/SparseVoxelOctree

## 功能概述

计算着色器,将排序后的位置码对应的 SVO 偏移写入哈希表。排序后的位置码存储在缓冲区末尾(有效码排在尾部,无效码排在头部),此着色器遍历有效位置码,将其在排序数组中的索引作为 SVO 偏移写入哈希表。

## 常量缓冲区

| 变量 | 类型 | 说明 |
|------|------|------|
| `gLocationCodeStartOffset` | `uint` | 排序缓冲区中有效位置码的起始偏移 |
| `gVoxelCount` | `uint` | 有效体素数量 |

## 资源绑定

| 资源 | 类型 | 说明 |
|------|------|------|
| `gVoxelHashTable` | `ParameterBlock<SDFSVOHashTable>` | 体素哈希表 |
| `gLocationCodes` | `RWByteAddressBuffer` | 排序后的位置码缓冲区 |

## 依赖关系

### import
- `Scene.SDFs.SparseVoxelOctree.SDFSVOHashTable`

## 实现细节

- 线程组大小 256x1x1
- 从偏移 `gLocationCodeStartOffset + threadID` 读取位置码
- 调用 `setSVOOffset(locationCode, threadID)` 将线程索引作为 SVO 偏移写入哈希表
- 这确保了 SVO 中的元素顺序与排序后的位置码顺序一致
