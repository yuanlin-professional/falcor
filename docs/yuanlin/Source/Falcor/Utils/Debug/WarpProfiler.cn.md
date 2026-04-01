# WarpProfiler 源码文档
> 路径: `Source/Falcor/Utils/Debug/WarpProfiler.h` + `Source/Falcor/Utils/Debug/WarpProfiler.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/Debug

## 功能概述

提供 Warp 级别的性能分析工具，用于测量 GPU 着色器中 Warp 的线程利用率和数据发散度。通过在着色器中调用 `warpUtilization()` 或 `warpDivergence()` 收集直方图数据，主机端可异步回读数据并查询指定 bin 范围的 Warp 直方图，或将所有直方图导出为 CSV 文件。

Warp 大小固定为 32（面向 NVIDIA GPU）。每个 bin 对应一个 32 桶的直方图，桶索引 `i` 表示有 `i+1` 个活跃线程的 Warp 数量。

## 类与接口

### `WarpProfiler`

- **继承**: 无
- **职责**: 管理 Warp 分析的完整流程，包括 GPU 缓冲区创建、着色器数据绑定、分析会话控制（begin/end）、数据异步回读及结果查询与导出。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `WarpProfiler(ref<Device> pDevice, const uint32_t binCount)` | 构造函数。创建 GPU 围栏、直方图缓冲区和回读暂存缓冲区。缓冲区大小为 `binCount * 32` 个 `uint32_t`。 |
| `void bindShaderData(const ShaderVar& var) const` | 将直方图缓冲区绑定到着色器变量 `gWarpHistogram`。必须在使用分析器前调用。 |
| `void begin(RenderContext* pRenderContext)` | 开始分析会话。清零 GPU 直方图缓冲区。 |
| `void end(RenderContext* pRenderContext)` | 结束分析会话。将 GPU 数据复制到暂存缓冲区，提交命令并插入 Fence 信号。 |
| `std::vector<uint32_t> getWarpHistogram(const uint32_t binIndex, const uint32_t binCount = 1)` | 获取指定 bin 范围的聚合 Warp 直方图。返回长度为 32 的数组。自动触发数据回读。 |
| `bool saveWarpHistogramsAsCSV(const std::filesystem::path& path)` | 将所有 bin 的直方图数据保存为 CSV 文件。每行一个 bin，列用分号分隔。返回是否成功。 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `kWarpSize` | `static constexpr size_t` | Warp 大小常量，值为 32 |
| `mpFence` | `ref<Fence>` | GPU 围栏，用于同步回读操作 |
| `mpHistogramBuffer` | `ref<Buffer>` | GPU 上的直方图结构化缓冲区（ShaderResource + UnorderedAccess） |
| `mpHistogramStagingBuffer` | `ref<Buffer>` | 用于回读的暂存缓冲区（ReadBack 内存类型） |
| `mBinCount` | `const uint32_t` | 分析 bin 的数量 |
| `mHistograms` | `vector<uint32_t>` | CPU 端存储的所有 bin 的直方图数据 |
| `mActive` | `bool` | 标记是否在 begin()/end() 会话中 |
| `mDataWaiting` | `bool` | 标记暂存缓冲区中是否有待回读的数据 |

## 依赖关系

### 本文件引用

- `Core/Macros.h` — Falcor 宏定义（`FALCOR_API`、`FALCOR_CHECK`）
- `Core/API/Buffer.h` — GPU 缓冲区
- `Core/API/Fence.h` — GPU 围栏
- `Core/API/Device.h` — GPU 设备
- `Core/API/RenderContext.h` — 渲染上下文
- `Core/Program/ShaderVar.h` — 着色器变量绑定
- `<filesystem>` — 文件路径
- `<fstream>` — 文件输出流

### 被以下文件引用

- 需要进行 Warp 利用率分析的渲染通道和着色器程序

## 实现细节

- **缓冲区布局**: 直方图缓冲区为一维数组，逻辑上组织为 `binCount` 行 x `kWarpSize`（32）列。每个元素为 `uint32_t`，存储对应 bin 和活跃线程数的 Warp 计数。
- **异步回读**: 采用与 `PixelDebug` 相似的 Fence 同步模式。`end()` 提交复制命令并发送信号，`readBackData()` 等待信号后通过 `map()`/`unmap()` 读取数据。
- **延迟回读**: `readBackData()` 在 `getWarpHistogram()` 或 `saveWarpHistogramsAsCSV()` 调用时按需触发，避免不必要的同步等待。
- **直方图聚合**: `getWarpHistogram()` 支持跨多个 bin 聚合直方图数据，将指定范围内各 bin 同一桶的计数累加。
- **CSV 格式**: 每行对应一个 bin，32 个桶值用分号 `;` 分隔。
