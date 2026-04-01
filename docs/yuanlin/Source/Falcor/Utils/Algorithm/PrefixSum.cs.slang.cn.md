# PrefixSum.cs.slang 源码文档

> 路径: `Source/Falcor/Utils/Algorithm/PrefixSum.cs.slang`
> 类型: 计算着色器 (Compute Shader)
> 模块: Utils/Algorithm

## 功能概述

GPU 端的并行前缀和计算着色器实现，基于 Blelloch 的经典并行扫描算法。使用独占扫描（exclusive scan）原地计算前缀和。

算法参考：
- G. Blelloch, "Vector Models for Data-Parallel Computing", MIT Press, 1990
- NVIDIA GPU Gems 3, Chapter 39

宿主端通过以下宏定义控制行为：
- `GROUP_SIZE`：线程组大小，必须为 2 的幂且 <= 1024

## 结构体与接口

### 常量缓冲区 `CB`

| 字段 | 类型 | 说明 |
|------|------|------|
| `gNumGroups` | `uint` | 待处理的线程组数量，每组处理 2N 个元素 |
| `gTotalNumElems` | `uint` | 元素总数，不要求是线程组大小的整数倍 |
| `gIter` | `uint` | 当前迭代索引（元素总数超过 2*GROUP_SIZE^2 时需要多次迭代） |

### 资源绑定

| 名称 | 类型 | 说明 |
|------|------|------|
| `gData` | `RWByteAddressBuffer` | 数据缓冲区（原地读写） |
| `gPrefixGroupSums` | `RWByteAddressBuffer` | 每组一个 uint，存储该组及左侧所有元素的总和 |
| `gTotalSum` | `RWByteAddressBuffer` | 当前前缀和迭代的总和 |
| `gPrevTotalSum` | `ByteAddressBuffer` | 上一次迭代的总和（只读） |
| `gSharedData` | `groupshared uint[2 * GROUP_SIZE]` | 共享内存中的临时工作缓冲区 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void groupScan(uint3 groupID, uint3 groupThreadID)` | 第一趟：组内并行前缀和。每个线程加载两个元素到共享内存，执行上扫（归约）和下扫（分发）两个阶段，并使用原子操作累加组前缀和 |
| `void finalizeGroups(uint3 groupID, uint3 groupThreadID)` | 第二趟：组间合并。将每个组的前缀和叠加到对应元素上，跳过第一组的 2N 个元素 |

## 依赖关系

### import

无外部着色器依赖。

## 实现细节

- **Blelloch 扫描算法**:
  1. **上扫阶段（Reduction）**: 从叶子到根逐层归约，计算部分和。迭代 d = N, N/2, ..., 1
  2. **清零根节点**: 将共享内存最后一个元素置零（独占扫描的关键步骤）
  3. **下扫阶段（Down-sweep）**: 从根到叶子逐层分发，完成前缀和计算。迭代 d = 1, 2, ..., N
- **数据加载**: 每个线程加载两个元素（一个在前半段，一个在后半段），越界元素填充为零
- **组间前缀和**: 使用 `InterlockedAdd` 原子操作在第一趟中累加各组总和。每个组的线程对所有 ID >= 自身组ID 的组前缀和进行原子加法。这种方式对中等规模输入（数十到数百个组）效率较高
- **多迭代支持**: 通过 `gIter` 和 `gPrevTotalSum` 支持超大数组的分段处理，每次迭代结束后将总和传递给下一次迭代
- **同步**: 在上扫和下扫的每一步之间使用 `GroupMemoryBarrierWithGroupSync()` 确保共享内存的数据一致性
