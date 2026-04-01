# OptixUtils 源码文档

> 路径: `Source/RenderPasses/OptixDenoiser/OptixUtils.h` + `OptixUtils.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/OptixDenoiser

## 功能概述

本文件提供 OptiX 上下文初始化工具和 CUDA GPU 缓冲区管理工具类。`CudaBuffer` 类封装了 CUDA 设备内存的分配和释放操作，`initOptix` 函数负责初始化 CUDA 设备和 OptiX 上下文。

## 类与接口

### `CudaBuffer`

- **继承**: 无
- **职责**: 管理 CUDA 设备端 GPU 缓冲区的生命周期

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `CudaBuffer()` | 默认构造函数 |
| `CUdeviceptr getDevicePtr()` | 获取 CUDA 设备指针 |
| `size_t getSize()` | 获取缓冲区大小（字节） |
| `void allocate(size_t size)` | 分配指定大小的 CUDA 设备内存，如已有则先释放 |
| `void resize(size_t size)` | 重新分配缓冲区（等同于 allocate） |
| `void free()` | 释放 CUDA 设备内存 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mSizeBytes` | `size_t` | 缓冲区大小（字节） |
| `mpDevicePtr` | `void*` | CUDA 设备内存指针 |

### 全局函数

| 函数签名 | 说明 |
|----------|------|
| `OptixDeviceContext initOptix(Falcor::Device* pDevice)` | 初始化 CUDA 设备和 OptiX 上下文，设置日志回调，失败时抛出异常 |

## 依赖关系

### 本文件引用

- `Falcor.h` — Falcor 核心框架
- `Utils/CudaRuntime.h` — CUDA 运行时头文件
- `Utils/CudaUtils.h` — CUDA 工具函数
- `<optix.h>` / `<optix_stubs.h>` — OptiX SDK 头文件
- `<optix_function_table_definition.h>` — OptiX 函数表定义（仅在 .cpp 中包含一次）
- `<cuda_runtime.h>` — CUDA 运行时

### 被以下文件引用

- `OptixDenoiser.h` — 被降噪器主类引用

## 实现细节

1. **错误检查宏**: 定义了 `OPTIX_CHECK` 和 `CUDA_CHECK` 宏，在 OptiX 或 CUDA 调用失败时抛出包含错误名称和描述的异常。

2. **OptiX 初始化流程**: `initOptix` 函数首先初始化 CUDA 设备（`pDevice->initCudaDevice()`），然后调用 `optixInit()` 初始化 OptiX，接着创建 OptiX 设备上下文并设置日志回调函数（日志级别 4）。

3. **日志回调**: `optixLogCallback` 将 OptiX 日志消息转发到 Falcor 的日志系统（作为警告级别）。

4. **设计来源**: `CudaBuffer` 类改编自 Ingo Wald 的 SIGGRAPH 2019 OptiX 7 教程代码。
