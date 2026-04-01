# CopySurface 源码文档

> 路径: `Source/Samples/CudaInterop/CopySurface.h` + `Source/Samples/CudaInterop/CopySurface.cu`
> 类型: CUDA 头文件 + 实现
> 模块: Samples/CudaInterop

## 功能概述

提供一个简单的 CUDA 内核,用于将输入表面的像素数据逐像素复制到输出表面。该文件是 Falcor-CUDA 互操作示例的核心计算部分,演示了如何在 CUDA 内核中读写 `cudaSurfaceObject_t`。

## 函数

### `copySurface<T>` (CUDA 内核)

| 参数 | 类型 | 说明 |
|------|------|------|
| `input` | `cudaSurfaceObject_t` | 输入表面对象 |
| `output` | `cudaSurfaceObject_t` | 输出表面对象 |
| `width` | `unsigned int` | 纹理宽度(像素) |
| `height` | `unsigned int` | 纹理高度(像素) |

- **模板参数 `T`**: 像素数据类型(`float` 或 `int`),根据纹理通道格式选择
- **职责**: 每个线程处理一个像素,使用 `surf2Dread` 从输入表面读取,`surf2Dwrite` 写入输出表面
- **线程块配置**: 16x16

### `launchCopySurface` (主机包装函数)

| 参数 | 类型 | 说明 |
|------|------|------|
| `input` | `cudaSurfaceObject_t` | 输入表面对象 |
| `output` | `cudaSurfaceObject_t` | 输出表面对象 |
| `width` | `unsigned int` | 纹理宽度 |
| `height` | `unsigned int` | 纹理高度 |
| `format` | `unsigned int` | 通道格式(`cudaChannelFormatKindFloat` 或其他) |

- **职责**: 根据 `format` 参数选择浮点或整数模板实例启动 CUDA 内核
- **网格配置**: `dimBlock(16, 16)`, `dimGrid` 根据纹理尺寸计算以覆盖所有像素

## 依赖关系

### 本文件引用

- `CopySurface.h` — 自身头文件
- `<device_launch_parameters.h>` — CUDA 设备启动参数
- `<cuda_runtime.h>` — CUDA 运行时(头文件中)

### 被以下文件引用

- `CudaInterop.cpp` — 在每帧渲染时调用 `launchCopySurface`

## 实现细节

- 使用模板函数实现对不同像素格式的支持,避免运行时分支。
- 线程块大小为 16x16 = 256 线程,是 CUDA 常见的二维网格配置。
- 网格大小向上取整以确保覆盖所有像素,内核内部通过边界检查避免越界访问。
