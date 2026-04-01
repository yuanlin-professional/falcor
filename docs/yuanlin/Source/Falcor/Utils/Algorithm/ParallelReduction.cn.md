# ParallelReduction 源码文档

> 路径: `Source/Falcor/Utils/Algorithm/ParallelReduction.h` + `Source/Falcor/Utils/Algorithm/ParallelReduction.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/Algorithm

## 功能概述

实现了对纹理所有像素的 GPU 并行归约（Parallel Reduction）操作。支持两种归约类型：求和（Sum）和最小/最大值（MinMax）。归约以 1024 个元素为一组递归执行，总迭代次数为 ceil(log2(N)/10)，其中 N 为总像素数。

数值精度介于成对求和（pairwise summation）和朴素累加（naive running summation）之间。

## 类与接口

### `ParallelReduction`

- **继承**: 无
- **职责**: 管理并行归约的计算着色器管线，对输入纹理执行全像素归约操作

#### `Type` 枚举

| 值 | 说明 |
|----|------|
| `Sum` | 求和归约，结果缓冲区需要 16 字节 |
| `MinMax` | 最小/最大值归约，结果缓冲区需要 32 字节 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `ParallelReduction(ref<Device> pDevice)` | 构造函数，创建初始通道和最终通道的计算着色器程序 |
| `template<typename T> void execute(RenderContext*, const ref<Texture>&, Type, T*, ref<Buffer>, uint64_t)` | 执行并行归约。T 必须与纹理格式兼容：float4 对应浮点格式，uint4 对应无符号整数格式，int4 对应有符号整数格式 |
| `uint64_t getMemoryUsageInBytes() const` | 返回中间缓冲区的内存使用量（字节） |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `ref<Device>` | GPU 设备引用 |
| `mpState` | `ref<ComputeState>` | 计算管线状态 |
| `mpInitialProgram` | `ref<Program>` | 初始通道着色器程序（2D 调度，按 32x32 像素块归约） |
| `mpFinalProgram` | `ref<Program>` | 最终通道着色器程序（1D 调度，递归归约中间结果） |
| `mpVars` | `ref<ProgramVars>` | 着色器变量绑定 |
| `mpBuffers[2]` | `ref<Buffer>[2]` | 中间结果缓冲区，用于多轮归约迭代 |

## 依赖关系

### 本文件引用

- `Core/Macros.h` — 平台宏定义
- `Core/API/Buffer.h` — 缓冲区管理
- `Core/State/ComputeState.h` — 计算管线状态
- `Core/Program/Program.h` — 着色器程序管理
- `Core/Program/ProgramVars.h` — 着色器变量绑定
- `Core/API/RenderContext.h` — 渲染上下文
- `Utils/Math/Common.h` — 数学工具函数
- `Utils/Timing/Profiler.h` — 性能分析
- `Utils/Algorithm/ParallelReductionType.slangh` — 归约类型和格式类型的共享宏定义
- `Utils/Algorithm/ParallelReduction.cs.slang` — GPU 端计算着色器

### 被以下文件引用

- 需要对纹理进行全局统计（如亮度总和、像素极值）的渲染通道

## 实现细节

- **两通道架构**:
  1. **初始通道（initialPass）**: 以 32x32 像素块为单位读取输入纹理，每个块归约为一个结果，存入中间缓冲区 0
  2. **最终通道（finalPass）**: 以 1024 元素为一组递归归约中间缓冲区，使用双缓冲区 ping-pong 策略直到归约为单个结果
- **格式检测**: 运行时检查输入纹理格式（Float/Sint/Uint），通过宏定义 `FORMAT_TYPE`、`FORMAT_CHANNELS`、`REDUCTION_TYPE` 控制着色器行为
- **模板实例化**: 显式实例化了 `float4`、`int4`、`uint4` 三种类型，分别对应浮点、有符号整数和无符号整数纹理格式
- **结果输出**: 支持同时将结果写入 GPU 缓冲区（异步，推荐用于性能）和读回 CPU（需要 GPU 刷新）
- **中间缓冲区**: 按需分配，仅在元素数量增加时重新分配
