# CudaInterop 源码文档

> 路径: `Source/Samples/CudaInterop/CudaInterop.h` + `Source/Samples/CudaInterop/CudaInterop.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Samples/CudaInterop

## 功能概述

本示例演示了 Falcor 与 CUDA 之间的互操作功能。程序将一张纹理加载到 GPU 中,通过 CUDA 内核将输入纹理的数据复制到输出纹理,最后将输出纹理渲染到屏幕上。该示例展示了如何在 Falcor 的 D3D12 渲染管线与 CUDA 计算之间共享 GPU 资源(纹理/表面对象)。

## 类与接口

### `CudaInterop`

- **继承**: `SampleApp`
- **职责**: 管理 CUDA 设备初始化、纹理创建与映射、以及每帧调用 CUDA 内核进行表面复制

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `CudaInterop(const SampleAppConfig& config)` | 构造函数,传入应用配置 |
| `~CudaInterop()` | 析构函数 |
| `void onLoad(RenderContext* pRenderContext) override` | 初始化 CUDA 设备,创建输入/输出纹理,将纹理映射为 CUDA 表面对象 |
| `void onFrameRender(RenderContext* pRenderContext, const ref<Fbo>& pTargetFbo) override` | 每帧调用 CUDA 内核复制表面,并将输出纹理 blit 到目标帧缓冲区 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mWidth` | `uint32_t` | 纹理宽度 |
| `mHeight` | `uint32_t` | 纹理高度 |
| `mpInputTex` | `ref<Texture>` | 输入纹理(从文件加载) |
| `mpOutputTex` | `ref<Texture>` | 输出纹理(用于接收 CUDA 内核的写入结果) |
| `mInputSurf` | `cudaSurfaceObject_t` | 输入纹理对应的 CUDA 表面对象 |
| `mOutputSurf` | `cudaSurfaceObject_t` | 输出纹理对应的 CUDA 表面对象 |

## 依赖关系

### 本文件引用

- `Falcor.h` — Falcor 核心框架
- `Core/SampleApp.h` — 示例应用基类
- `CopySurface.h` — CUDA 表面复制内核的声明
- `Core/AssetResolver.h` — 资源路径解析
- `Utils/CudaUtils.h` — CUDA 工具函数(纹理到表面的映射)
- `<cuda.h>`, `<cuda_runtime.h>` — CUDA 运行时头文件

### 被以下文件引用

- 无(顶层示例入口)

## 实现细节

- 在 `onLoad` 中通过 `getDevice()->initCudaDevice()` 初始化 CUDA 设备。
- 使用 `Texture::createFromFile` 加载 `test_images/smoke_puff.png` 作为输入纹理,绑定标志为 `ResourceBindFlags::Shared` 以支持 CUDA 共享。
- 输出纹理同样使用 `Shared` 绑定标志创建,额外添加 `ShaderResource` 以支持 blit 操作。
- 通过 `cuda_utils::mapTextureToSurface` 将 D3D12 纹理映射为 CUDA 表面对象(每个资源只能调用一次)。
- 每帧在 `onFrameRender` 中根据纹理格式(浮点或整数)选择合适的模板参数调用 `launchCopySurface` CUDA 内核。
- 程序入口使用 `catchAndReportAllExceptions` 包装以确保异常安全。
