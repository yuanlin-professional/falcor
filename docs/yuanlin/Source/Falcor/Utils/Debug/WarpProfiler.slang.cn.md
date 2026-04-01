# WarpProfiler.slang 源码文档
> 路径: `Source/Falcor/Utils/Debug/WarpProfiler.slang`
> 类型: Slang 着色器文件
> 模块: Utils/Debug

## 功能概述

实现 Warp 分析器的 GPU 端逻辑，提供两个核心函数用于测量 Warp 级别的线程利用率和数据发散度。着色器程序需编译为 Shader Model 6.5 或更高版本。实现假设 Warp 大小为 32（面向 NVIDIA GPU）。

## 结构体与接口

无自定义结构体或接口。

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void warpUtilization(uint binIndex)` | 测量当前 Warp 中的活跃线程数。使用 `WaveActiveCountBits(true)` 统计活跃线程数量，由首线程（`WaveIsFirstLane()`）通过原子操作将结果写入直方图缓冲区对应桶中。 |
| `void warpDivergence(uint binIndex, uint value)` | 测量当前 Warp 中指定值的发散程度。使用 `WaveMatch(value)` 识别具有相同值的线程组，每组的首线程调用 `warpUtilization()` 记录该组的活跃线程数，从而间接统计不同值的数量。 |

### 全局变量

| 变量 | 类型 | 说明 |
|------|------|------|
| `gWarpHistogram` | `RWStructuredBuffer<uint>` | 全局直方图缓冲区，由主机端 `WarpProfiler::bindShaderData()` 绑定 |

## 依赖关系

### import

无（该文件不依赖其他 Slang 模块）

### 被以下文件引用

- 任何需要 Warp 分析功能的着色器程序（通过 `import WarpProfiler` 引入）

## 实现细节

- **直方图索引计算**: `binIndex * 32 + activeCount - 1`。每个 bin 占用 32 个桶，桶索引 0 对应 1 个活跃线程，桶索引 31 对应 32 个活跃线程。
- **Wave Intrinsics**:
  - `WaveActiveCountBits(true)` — 统计 Warp 中所有活跃线程数量。
  - `WaveIsFirstLane()` — 判断当前线程是否为 Warp 中的首个活跃线程，用于避免重复写入。
  - `WaveMatch(value)` — 返回位掩码，标识 Warp 中与当前线程持有相同值的所有线程。
  - `WaveGetLaneIndex()` — 获取当前线程在 Warp 中的 Lane 索引。
- **原子写入**: 使用 `InterlockedAdd` 对直方图缓冲区进行原子累加，确保多 Warp 并发写入时的正确性。
- **发散度测量原理**: `warpDivergence()` 通过 `WaveMatch` 将 Warp 分组后，每组独立调用 `warpUtilization()`，使得直方图反映的是各分组的大小分布，而非整体活跃线程数。发散度越高，组越多且越小。
- **Shader Model 要求**: 需要 SM 6.5 或以上版本，因为使用了 `WaveMatch` 等高级 Wave Intrinsics。
