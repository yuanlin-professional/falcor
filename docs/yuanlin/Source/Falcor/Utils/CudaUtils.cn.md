# CudaUtils 源码文档

> 路径: `Source/Falcor/Utils/CudaUtils.h` + `Source/Falcor/Utils/CudaUtils.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils

## 功能概述

提供 CUDA 与 Falcor 图形 API（D3D12/Vulkan）之间的互操作工具。包括 CUDA 设备管理、内存分配、外部内存/信号量导入、缓冲区和纹理的跨 API 共享等功能。

## 类与接口

### `cuda_utils::CudaDevice`

- **继承**: `Object`
- **职责**: 封装 CUDA 设备、上下文和流，在与 Falcor 设备相同的 GPU 适配器上创建 CUDA 设备

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `CudaDevice(const Device* pDevice)` | 构造函数，根据 Falcor 设备查找匹配的 CUDA 设备 |
| `CUdevice getDevice() const` | 获取 CUDA 设备句柄 |
| `CUcontext getContext() const` | 获取 CUDA 上下文 |
| `CUstream getStream() const` | 获取 CUDA 流 |

### `cuda_utils::ExternalMemory`

- **继承**: `Object`
- **职责**: 封装外部内存资源，将 Falcor 缓冲区导入为 CUDA 可访问的外部内存

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `ExternalMemory(ref<Resource> pResource)` | 从 Falcor 资源导入外部内存 |
| `size_t getSize() const` | 获取内存大小 |
| `void* getMappedData() const` | 获取映射后的设备指针 |

### `cuda_utils::ExternalSemaphore`

- **继承**: `Object`
- **职责**: 封装外部信号量，实现 CUDA 与 Falcor 之间的同步

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `ExternalSemaphore(ref<Fence> pFence)` | 从 Falcor Fence 导入外部信号量 |
| `void signal(uint64_t value, cudaStream_t stream)` | 在 CUDA 端发出信号 |
| `void wait(uint64_t value, cudaStream_t stream)` | 在 CUDA 端等待信号 |
| `void waitForCuda(CopyContext*, cudaStream_t, uint64_t)` | 让 Falcor 等待 CUDA 完成 |
| `void waitForFalcor(CopyContext*, cudaStream_t, uint64_t)` | 让 CUDA 等待 Falcor 完成 |

### `InteropBuffer` 结构体

- **职责**: 封装 DX/Vulkan 与 CUDA 互操作的缓冲区数据

### 命名空间级工具函数

| 函数签名 | 说明 |
|----------|------|
| `void deviceSynchronize()` | 同步 CUDA 设备 |
| `void* mallocDevice(size_t size)` | 在 CUDA 设备上分配内存 |
| `void freeDevice(void* devPtr)` | 释放 CUDA 设备内存 |
| `void memcpyDeviceToDevice(...)` | 设备到设备内存拷贝 |
| `void memcpyHostToDevice(...)` | 主机到设备内存拷贝 |
| `void memcpyDeviceToHost(...)` | 设备到主机内存拷贝 |
| `void memsetDevice(...)` | 设备内存填充 |
| `cudaExternalMemory_t importExternalMemory(const Buffer*)` | 将 Falcor 缓冲区导入为 CUDA 外部内存 |
| `void* getSharedDevicePtr(...)` | 获取共享资源的 CUDA 设备指针 |
| `cudaMipmappedArray_t importTextureToMipmappedArray(...)` | 将纹理导入为 CUDA mipmap 数组 |
| `cudaSurfaceObject_t mapTextureToSurface(...)` | 将纹理映射为 CUDA 表面对象 |

## 依赖关系

### 本文件引用

- `Core/API/Device.h`、`Core/API/Buffer.h`、`Core/API/Texture.h`、`Core/API/Fence.h` — 图形 API 抽象
- `CudaRuntime.h` — CUDA 运行时包装
- `<cuda.h>` — CUDA 驱动 API

### 被以下文件引用

- OptiX 集成模块、需要 GPU 计算与渲染互操作的模块

## 实现细节

- 设备匹配优先通过 LUID 查找，失败后回退到设备名称匹配，最后尝试首个兼容设备
- 支持 D3D12 和 Vulkan 两种后端的外部内存/信号量导入
- Windows 使用 Win32 句柄，Linux 使用文件描述符
- 提供 `FALCOR_CUDA_CHECK` 和 `FALCOR_CU_CHECK` 错误检查宏
