# BitonicSort 源码文档

> 路径: `Source/Falcor/Utils/Algorithm/BitonicSort.h` + `Source/Falcor/Utils/Algorithm/BitonicSort.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/Algorithm

## 功能概述

实现基于 GPU 的原地双调排序（Bitonic Sort）算法，以分块方式对 N 个元素进行排序。双调排序的时间复杂度为 O(N*log^2(N))，具有极佳的并行性和几乎无分支的特性，非常适合在 GPU 上对较短序列进行高效排序。

该实现依赖 NVIDIA GPU 和 NVAPI，使用 warp 内水平操作（horizontal operations）和共享内存（shared memory）跨 warp 进行数据交换。

## 类与接口

### `BitonicSort`

- **继承**: 无
- **职责**: 封装 GPU 双调排序的计算着色器管线，提供原地排序接口

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `BitonicSort(ref<Device> pDevice)` | 构造函数，创建计算状态和着色器程序。若 NVAPI 不可用则抛出异常 |
| `bool execute(RenderContext* pRenderContext, ref<Buffer> pData, uint32_t totalSize, uint32_t chunkSize, uint32_t groupSize = 256)` | 执行原地双调排序。每个分块独立升序排序。`chunkSize` 必须为 2 的幂且在 [1, groupSize] 范围内，`groupSize` 必须为 2 的幂且在 [1, 1024] 范围内 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `ref<Device>` | GPU 设备引用 |
| `mSort.pState` | `ref<ComputeState>` | 计算管线状态 |
| `mSort.pProgram` | `ref<Program>` | 计算着色器程序 |
| `mSort.pVars` | `ref<ProgramVars>` | 着色器程序变量绑定 |

## 依赖关系

### 本文件引用

- `Core/Macros.h` — 平台宏定义
- `Core/State/ComputeState.h` — 计算管线状态
- `Core/Program/Program.h` — 着色器程序管理
- `Core/Program/ProgramVars.h` — 着色器变量绑定
- `Core/API/RenderContext.h` — 渲染上下文（用于 dispatch）
- `Utils/Math/Common.h` — 数学工具函数（如 `div_round_up`、`isPowerOf2`）
- `Utils/Timing/Profiler.h` — 性能分析工具
- `Utils/Algorithm/BitonicSort.cs.slang` — GPU 端计算着色器

### 被以下文件引用

- 需要在 GPU 上进行短序列排序的渲染通道或算法模块

## 实现细节

- **着色器编译优化**: 构造时使用占位的 `CHUNK_SIZE` 和 `GROUP_SIZE` 宏定义获取反射数据，在 `execute()` 中设置实际值。当遇到新的分块大小时会触发重新编译。
- **调度策略**: 将线程组数量沿 X 和 Y 两个维度展开（近似方形布局），以支持大量线程组的调度。通过常量缓冲区传递 `gTotalSize` 和 `gDispatchX` 避免频繁重编译。
- **边界处理**: `totalSize` 不要求是 `chunkSize` 的整数倍，着色器中越界元素被设为 `UINT_MAX` 以确保排序后位于末尾。
- **NVAPI 依赖**: 构造函数中检查 `FALCOR_NVAPI_AVAILABLE`，不可用时直接抛出异常。
