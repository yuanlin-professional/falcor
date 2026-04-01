# ParallelReduction.cs.slang 源码文档

> 路径: `Source/Falcor/Utils/Algorithm/ParallelReduction.cs.slang`
> 类型: 计算着色器 (Compute Shader)
> 模块: Utils/Algorithm

## 功能概述

GPU 端的并行归约计算着色器实现，支持求和（Sum）和最小/最大值（MinMax）两种归约操作。利用 Wave 内置函数（`WaveActiveSum`、`WaveActiveMin`、`WaveActiveMax`）进行 warp 级归约，并通过组共享内存完成跨 warp 的归约。

宿主端通过以下宏定义控制行为：
- `FORMAT_CHANNELS`：数据分量数（1-4）
- `FORMAT_TYPE`：纹理格式类型（浮点/有符号整数/无符号整数）
- `REDUCTION_TYPE`：归约操作类型（求和/最小最大值）

## 结构体与接口

### 常量缓冲区 `PerFrameCB`

| 字段 | 类型 | 说明 |
|------|------|------|
| `gResolution` | `uint2` | 输入纹理的像素尺寸 |
| `gNumTiles` | `uint2` | 输入纹理中的块数量 |
| `gElems` | `uint` | 中间缓冲区中的元素数量（最终通道使用） |

### 资源绑定

| 名称 | 类型 | 说明 |
|------|------|------|
| `gInput` | `Texture2D<DataType>` | 输入纹理 |
| `gInputBuffer` | `Buffer<DataType>` | 中间输入缓冲区（最终通道使用） |
| `gResult` | `RWBuffer<DataType>` | 归约结果输出缓冲区 |
| `gIntermediateCache` | `groupshared DataType[]` | 组共享内存缓存，用于跨 warp 归约 |

### 类型定义

| 名称 | 说明 |
|------|------|
| `DataType` | 根据 `FORMAT_TYPE` 条件编译为 `float4`、`int4` 或 `uint4` |
| `kMaxValue` | 数据类型的最大值常量 |
| `kMinValue` | 数据类型的最小值常量 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `DataType loadTexture(uint2 pixelCoords)` | 从纹理加载像素值，对于少于 4 分量的格式将第 4 分量置零 |
| `void reduceSum(DataType value, uint dstIdx, uint groupThreadIdx)` | 线程组内求和归约：先用 `WaveActiveSum` 进行 warp 内求和，再通过共享内存跨 warp 求和 |
| `void reduceMinMax(DataType minValue, DataType maxValue, uint dstIdx, uint groupThreadIdx)` | 线程组内最小/最大值归约：先用 `WaveActiveMin/Max` 进行 warp 内归约，再跨 warp 归约 |
| `void initialPass(uint3 globalThreadId, uint groupThreadIdx, uint3 groupId)` | 初始通道入口点 [32x32 线程]：从纹理加载像素并执行组内归约 |
| `void finalPass(uint3 globalThreadId, uint groupThreadIdx, uint3 groupId)` | 最终通道入口点 [1024x1 线程]：对中间缓冲区进行递归归约 |

## 依赖关系

### import

- `Utils/Algorithm/ParallelReductionType.slangh` — 归约类型和格式类型的宏定义
- `Utils/Math/MathConstants.slangh` — 数学常量（`FLT_MAX`、`INT32_MIN` 等）

## 实现细节

- **两级归约策略**:
  1. warp 级别：使用 `WaveActiveSum`/`WaveActiveMin`/`WaveActiveMax` 内置函数，由 GPU 硬件高效执行
  2. 组级别：warp 级结果写入 `gIntermediateCache` 共享内存，然后由前 32 个线程再做一轮 Wave 操作汇总
- **初始通道**: 以 32x32 线程组处理纹理，每个组生成一个归约结果。越界像素使用零（求和）或极值（MinMax）作为初始值
- **最终通道**: 以 1024 线程一维分组处理中间缓冲区，递归执行直到只剩一个结果
- **MinMax 存储**: 最小值和最大值交替存储在结果缓冲区中（`dstIdx*2` 和 `dstIdx*2+1`），共享内存缓存大小为求和模式的两倍
