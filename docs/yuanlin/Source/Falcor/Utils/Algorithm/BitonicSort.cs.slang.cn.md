# BitonicSort.cs.slang 源码文档

> 路径: `Source/Falcor/Utils/Algorithm/BitonicSort.cs.slang`
> 类型: 计算着色器 (Compute Shader)
> 模块: Utils/Algorithm

## 功能概述

GPU 端的双调排序计算着色器实现。对 32 位无符号整数值进行原地分块排序，每个分块内升序排列。利用 NVAPI 的 warp 级 shuffle 指令在 warp 内高效交换数据，并使用组共享内存在 warp 之间进行数据交换。

宿主端通过以下宏定义控制行为：
- `CHUNK_SIZE`（= N）：分块大小，必须为 2 的幂
- `GROUP_SIZE`：线程组大小，必须为 2 的幂且 <= 1024

当前实现假定 warp 大小为 32（NVIDIA GPU）。

## 结构体与接口

### 常量缓冲区 `CB`

| 字段 | 类型 | 说明 |
|------|------|------|
| `gTotalSize` | `uint` | 数据缓冲区中的元素总数 |
| `gDispatchX` | `uint` | 调度维度 X 方向的线程组数量 |

### 资源绑定

| 名称 | 类型 | 说明 |
|------|------|------|
| `gData` | `RWByteAddressBuffer` | 待排序的数据缓冲区（原地读写） |
| `gSharedData` | `groupshared uint[GROUP_SIZE * 2]` | 组共享内存中的临时工作缓冲区 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void bitonicSortInWarp(inout uint value, uint i, uint j, bool dir)` | warp 内双调排序，使用 `NvShflXor` 指令在 warp 内交换数据。处理迭代 {j, j/2, ..., 1}，其中 j <= 16 |
| `void main(uint3 groupID, uint groupIdx)` | 计算着色器入口点。加载数据、执行 warp 内排序（k=2..32），对于 CHUNK_SIZE > 32 的情况使用共享内存完成更大步长的排序，最后写回结果 |

## 依赖关系

### import

- `Utils/NVAPI.slangh` — 提供 `NvShflXor` warp 级 shuffle-xor 操作

## 实现细节

- **两阶段排序**:
  1. **warp 内阶段**（k = 2, 4, ..., 32）：利用 `NvShflXor` 在 warp 内高效完成小步长的双调比较交换
  2. **共享内存阶段**（k = 64, 128, ..., N）：通过 `gSharedData` 在 warp 之间交换数据，使用 ping-pong 策略（交替偏移地址）避免竞争条件
- **边界处理**: 越界元素设为 `UINT_MAX`（-1），确保排序后位于分块末尾
- **线程映射**: 元素与线程一一映射（CHUNK_SIZE <= GROUP_SIZE），每个线程处理一个元素
- **同步屏障**: 共享内存阶段在每次主步和次步之间使用 `GroupMemoryBarrierWithGroupSync()` 确保数据一致性
