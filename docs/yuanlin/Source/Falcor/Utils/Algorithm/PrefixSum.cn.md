# PrefixSum 源码文档

> 路径: `Source/Falcor/Utils/Algorithm/PrefixSum.h` + `Source/Falcor/Utils/Algorithm/PrefixSum.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/Algorithm

## 功能概述

实现了 GPU 上的并行前缀和（Prefix Sum / Exclusive Scan）算法。对 `uint32_t` 元素数组进行原地独占扫描（exclusive scan），计算结果为 y[i] = x[0] + x[1] + ... + x[i-1]（其中 y[0] = 0）。

该算法基于 Blelloch 的经典并行前缀和算法实现，采用两趟计算着色器：第一趟在线程组内计算局部前缀和，第二趟将组间前缀和叠加到每个元素上。

## 类与接口

### `PrefixSum`

- **继承**: 无
- **职责**: 管理 GPU 并行前缀和的计算管线，提供原地前缀和计算接口

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `PrefixSum(ref<Device> pDevice)` | 构造函数，创建计算着色器程序和临时缓冲区 |
| `void execute(RenderContext*, ref<Buffer> pData, uint32_t elementCount, uint32_t* pTotalSum, ref<Buffer> pTotalSumBuffer, uint64_t pTotalSumOffset)` | 对数据缓冲区执行原地独占前缀和。可选输出所有元素的总和到 CPU 或 GPU 缓冲区 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `ref<Device>` | GPU 设备引用 |
| `mpComputeState` | `ref<ComputeState>` | 计算管线状态 |
| `mpPrefixSumGroupProgram` | `ref<Program>` | 第一趟着色器程序（组内扫描） |
| `mpPrefixSumGroupVars` | `ref<ProgramVars>` | 第一趟着色器变量 |
| `mpPrefixSumFinalizeProgram` | `ref<Program>` | 第二趟着色器程序（组间合并） |
| `mpPrefixSumFinalizeVars` | `ref<ProgramVars>` | 第二趟着色器变量 |
| `mpPrefixGroupSums` | `ref<Buffer>` | 每个线程组的前缀和临时缓冲区 |
| `mpTotalSum` | `ref<Buffer>` | 当前迭代的总和临时缓冲区 |
| `mpPrevTotalSum` | `ref<Buffer>` | 上一次迭代的总和临时缓冲区 |

## 依赖关系

### 本文件引用

- `Core/Macros.h` — 平台宏定义
- `Core/API/Buffer.h` — 缓冲区管理
- `Core/State/ComputeState.h` — 计算管线状态
- `Core/Program/Program.h` — 着色器程序管理
- `Core/Program/ProgramVars.h` — 着色器变量绑定
- `Core/API/RenderContext.h` — 渲染上下文
- `Utils/Math/Common.h` — 数学工具函数（`div_round_up`）
- `Utils/Timing/Profiler.h` — 性能分析
- `Utils/Algorithm/PrefixSum.cs.slang` — GPU 端计算着色器

### 被以下文件引用

- 需要前缀和操作的渲染模块（如粒子系统、流式压缩等）

## 实现细节

- **线程组大小**: 固定为 1024（`kGroupSize`），每个线程组处理 2048 个元素
- **多迭代支持**: 当元素总数超过 `kGroupSize * kGroupSize * 2`（约 2M 个元素）时，自动分为多次迭代处理。每次迭代的总和通过 `mpPrevTotalSum` 传递给下一次迭代
- **两趟算法**:
  1. **第一趟（groupScan）**: 每个线程组对 2N 个元素执行 Blelloch 扫描，同时使用原子操作累加组前缀和
  2. **第二趟（finalizeGroups）**: 将各组的前缀和叠加到对应元素上（跳过第一组的 2N 个元素）
- **UAV 屏障**: 两趟之间插入 UAV 屏障确保第一趟的写入完成
- **结果输出**: 支持将总和写入指定 GPU 缓冲区（无需 GPU 刷新），也支持读回 CPU（需要 GPU 同步）
