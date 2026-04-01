# OptixDenoiser 源码文档

> 路径: `Source/RenderPasses/OptixDenoiser/OptixDenoiser.h` + `OptixDenoiser.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/OptixDenoiser

## 功能概述

本文件实现了基于 NVIDIA OptiX AI 的降噪渲染通道。该通道封装了 OptiX Denoiser，能够对含噪声的渲染图像进行实时降噪处理。支持 LDR、HDR 和时序（Temporal）降噪模式，并可选择性地使用反照率（albedo）、法线（normal）和运动向量（motion vector）作为辅助引导输入，以提升降噪质量。

该通道通过 DX 与 CUDA/OptiX 之间的互操作（interop）机制实现数据交换，使用中间缓冲区在 DirectX 纹理和 OptiX 所需的扁平数组之间进行格式转换。在 RTX 3090 上，1080p 分辨率下约耗时 3ms。

## 类与接口

### `OptixDenoiser_`

- **继承**: `RenderPass`
- **职责**: 封装 OptiX AI 降噪器，在 Falcor 渲染图中作为降噪渲染通道使用

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `OptixDenoiser_(ref<Device>, const Properties&)` | 构造函数，解析属性并创建格式转换着色器通道 |
| `Properties getProperties() const` | 序列化当前通道属性（enabled、blend、model、denoiseAlpha） |
| `RenderPassReflection reflect(const CompileData&)` | 声明输入输出：color（必需）、albedo/normal/mvec（可选）、output |
| `void compile(RenderContext*, const CompileData&)` | 初始化 OptiX 上下文，检测可用输入，分配中间缓冲区 |
| `void execute(RenderContext*, const RenderData&)` | 执行降噪：转换输入格式 -> 计算亮度/平均色 -> 调用 OptiX 降噪 -> 转换输出格式 |
| `void renderUI(Gui::Widgets&)` | 渲染 UI 控件：启用开关、模型选择、引导通道开关、alpha 降噪、混合滑块 |
| `void setScene(RenderContext*, const ref<Scene>&)` | 设置当前场景引用 |
| `void setupDenoiser()` | 创建/重建 OptiX 降噪器实例，分配 GPU 临时缓冲区 |
| `void convertTexToBuf(...)` | 将 DX 纹理转换为 OptiX 所需的扁平缓冲区 |
| `void convertNormalsToBuf(...)` | 将法线纹理转换到视图空间并写入缓冲区 |
| `void convertBufToTex(...)` | 将降噪输出缓冲区转换回 DX 纹理 |
| `void convertMotionVectors(...)` | 转换 Falcor 运动向量格式为 OptiX 格式（像素空间、方向取反） |
| `void allocateStagingBuffer(...)` | 分配 DX <-> CUDA 共享中间缓冲区 |
| `void freeStagingBuffer(...)` | 释放中间缓冲区 |
| `void reallocateStagingBuffers(...)` | 当分辨率变化时重新分配所有中间缓冲区 |
| `void* exportBufferToCudaDevice(ref<Buffer>&)` | 获取缓冲区的 CUDA 设备指针 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mEnabled` | `bool` | 降噪开关 |
| `mSelectBestMode` | `bool` | 是否自动选择最佳降噪模式 |
| `mIsFirstFrame` | `bool` | 创建/重建降噪器后的首帧标记 |
| `mHasColorInput` | `bool` | 是否连接了颜色输入 |
| `mHasAlbedoInput` | `bool` | 是否连接了反照率引导 |
| `mHasNormalInput` | `bool` | 是否连接了法线引导 |
| `mHasMotionInput` | `bool` | 是否连接了运动向量（用于时序降噪） |
| `mBufferSize` | `uint2` | 当前渲染分辨率 |
| `mSelectedModel` | `uint32_t` | 当前选择的降噪模型（LDR/HDR/Temporal） |
| `mOptixContext` | `OptixDeviceContext` | OptiX 设备上下文 |
| `mDenoiser` | 匿名结构体 | 封装所有 OptiX 降噪器参数、句柄、中间缓冲区和互操作数据 |
| `mpConvertTexToBuf` | `ref<ComputePass>` | 纹理到缓冲区转换的计算着色器 |
| `mpConvertNormalsToBuf` | `ref<ComputePass>` | 法线到缓冲区转换的计算着色器 |
| `mpConvertMotionVectors` | `ref<ComputePass>` | 运动向量转换的计算着色器 |
| `mpConvertBufToTex` | `ref<FullScreenPass>` | 缓冲区到纹理转换的全屏着色器 |

### `Interop`（嵌套结构体）

- **职责**: 封装 DX <-> CUDA 互操作数据

| 成员 | 类型 | 说明 |
|------|------|------|
| `buffer` | `ref<Buffer>` | Falcor 缓冲区 |
| `devicePtr` | `CUdeviceptr` | 缓冲区的 CUDA 设备指针 |

## 依赖关系

### 本文件引用

- `Falcor.h` — Falcor 核心框架
- `Core/Enum.h` — 枚举工具
- `Core/Pass/FullScreenPass.h` — 全屏渲染通道
- `RenderGraph/RenderPass.h` — 渲染通道基类
- `OptixUtils.h` — OptiX 初始化工具
- `Utils/CudaUtils.h` — CUDA 工具函数
- 着色器文件: `ConvertTexToBuf.cs.slang`、`ConvertNormalsToBuf.cs.slang`、`ConvertMotionVectorInputs.cs.slang`、`ConvertBufToTex.ps.slang`

### 被以下文件引用

- 渲染图配置脚本（Python）通过 `OptixDenoiser` 名称引用

## 实现细节

1. **降噪模式**: 支持 LDR、HDR、Temporal 和 AOV（AOV 尚未完全支持）四种 OptiX 降噪模型。默认使用 HDR 模式，当检测到运动向量输入时自动切换到 Temporal 模式。

2. **DX-CUDA 互操作**: 通过创建带 `ResourceBindFlags::Shared` 标志的 Falcor 缓冲区，然后获取其 CUDA 设备指针来实现跨 API 内存共享。使用 `waitForFalcor()` 和 `waitForCuda()` 进行同步。

3. **时序降噪**: 首帧时将当前帧同时用作当前输入和前帧输出，后续帧使用实际的前帧降噪结果。

4. **Python 脚本接口**: 通过 pybind11 暴露 `enabled` 属性。构造函数支持 `model`、`blend`、`denoiseAlpha` 等脚本参数。

5. **插件注册**: 通过 `registerPlugin` 导出函数注册为 Falcor 渲染通道插件。
