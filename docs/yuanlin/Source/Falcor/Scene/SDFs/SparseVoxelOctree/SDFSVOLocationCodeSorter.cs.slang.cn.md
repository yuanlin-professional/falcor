# SDFSVOLocationCodeSorter.cs.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SparseVoxelOctree/SDFSVOLocationCodeSorter.cs.slang`
> 类型: 计算着色器 (.cs.slang)
> 模块: Scene/SDFs/SparseVoxelOctree

## 功能概述

计算着色器,实现 64 位位置码的原地双调排序(Bitonic Merge Sort)。支持本地(组内)和全局(跨组)排序步骤,通过多次分发实现对任意大小缓冲区的完整排序。

## 常量缓冲区

| 变量 | 类型 | 说明 |
|------|------|------|
| `gAlgorithm` | `uint` | 当前排序算法步骤 |
| `gComparisonHeight` | `uint` | 当前比较高度 |

## 排序算法阶段

| 常量 | 说明 |
|------|------|
| `LOCAL_BMS` | 本地双调合并排序(组共享内存内) |
| `LOCAL_DISPERSE` | 本地分散步骤 |
| `BIG_FLIP` | 全局翻转步骤(跨组) |
| `BIG_DISPERSE` | 全局分散步骤(跨组) |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void localCompareSwap(uint a, uint b)` | 在组共享内存中比较交换两个元素 |
| `void globalCompareSwap(uint a, uint b)` | 在全局缓冲区中比较交换两个元素 |
| `void localFlip(uint, uint)` | 本地翻转操作 |
| `void localDisperse(uint, uint)` | 本地分散操作 |
| `void localBMS(uint, uint)` | 完整的本地双调合并排序 |
| `void bigFlip(uint, uint)` | 全局翻转操作 |
| `void bigDisperse(uint, uint)` | 全局分散操作 |

## 依赖关系

### 条件编译宏
- `GROUP_SIZE`, `BUFFER_SIZE`, `LOCAL_BMS`, `LOCAL_DISPERSE`, `BIG_FLIP`, `BIG_DISPERSE`

## 实现细节

- 基于 https://poniesandlight.co.uk/reflect/bitonic_merge_sort/ 改编
- 64 位位置码存储为 `uint2`,比较时先比较高 32 位再比较低 32 位
- 本地操作使用组共享内存 `gGroupBuffer[GROUP_SIZE * 2]`
- 排序策略:先在组内完成完整排序,然后通过全局翻转和分散步骤合并跨组数据
