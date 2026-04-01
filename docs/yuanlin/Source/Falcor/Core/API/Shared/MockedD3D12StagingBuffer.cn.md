# MockedD3D12StagingBuffer 源码文档

> 路径: `Source/Falcor/Core/API/Shared/MockedD3D12StagingBuffer.h` / `MockedD3D12StagingBuffer.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API/Shared

## 功能概述

实现一个模拟的 `ID3D12Resource` 接口，用于在 CPU 端接收数据写入并自动同步到 GPU 缓冲区。该类最初设计用于与 NVIDIA 的 DDGIVolume SDK 交互：DDGIVolume 的 `Update` 方法需要一个 D3D12 资源进行映射写入，而本类通过在 `Map` 时返回 CPU 内存、在 `Unmap` 时将数据上传到 GPU 缓冲区的方式，避免了从 GPU 回读数据的开销。仅在 `FALCOR_HAS_D3D12` 宏启用时编译。

## 类与接口

### `MockedD3D12StagingBuffer`
- **继承**: `ID3D12Resource`（COM 接口）
- **职责**: 模拟 D3D12 资源接口，在 CPU 和 GPU 之间提供透明的数据暂存和同步。SDK 写入 CPU 内存，解映射时自动上传到 GPU。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `void resize(ref<Device> pDevice, size_t size)` | 调整缓冲区大小，同时重新分配 CPU 内存和 GPU 缓冲区 |
| `size_t getSize() const` | 获取缓冲区大小 |
| `const void* getData() const` | 获取 CPU 端数据指针 |
| `HRESULT Map(UINT, const D3D12_RANGE*, void**)` | 映射操作，返回 CPU 内存指针（不涉及 GPU 映射） |
| `void Unmap(UINT, const D3D12_RANGE*)` | 解映射操作，将 CPU 数据通过 `setBlob` 上传到 GPU 缓冲区 |
| `D3D12_GPU_VIRTUAL_ADDRESS GetGPUVirtualAddress()` | 返回内部 GPU 缓冲区的虚拟地址 |

#### COM 接口方法（委托给内部 GPU 缓冲区）

| 方法 | 说明 |
|------|------|
| `QueryInterface` | 委托给内部 GPU 缓冲区的原生 D3D12 资源 |
| `AddRef` / `Release` | 委托给内部 GPU 缓冲区的原生 D3D12 资源 |
| `GetPrivateData` / `SetPrivateData` / `SetPrivateDataInterface` | 委托给内部 GPU 缓冲区 |
| `SetName` / `GetDevice` | 委托给内部 GPU 缓冲区 |
| `GetDesc` | 委托给内部 GPU 缓冲区 |
| `WriteToSubresource` / `ReadFromSubresource` | 委托给内部 GPU 缓冲区 |
| `GetHeapProperties` | 委托给内部 GPU 缓冲区 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mData` | `std::vector<uint8_t>` | CPU 端数据缓冲区 |
| `mpGpuBuffer` | `ref<Buffer>` | Falcor GPU 缓冲区对象，绑定标志为 Constant + ShaderResource，内存类型为 Upload |

## 依赖关系

### 本文件引用
- `Core/Macros.h` — 框架宏定义
- `Core/API/Buffer.h` — Falcor 缓冲区抽象
- `Core/API/Device.h` — GPU 设备接口
- `Core/API/NativeHandleTraits.h` — 原生句柄转换
- `<d3d12.h>` — D3D12 API
- `<windows.h>` — Windows API

### 被以下文件引用
- 主要供 DDGI 相关模块使用

## 实现细节

- **Map 实现**: 直接返回 `mData.data()`（CPU 内存地址），不执行任何 GPU 映射操作，使外部 SDK 可以安全地写入 CPU 缓冲区。
- **Unmap 实现**: 调用 `mpGpuBuffer->setBlob()` 将整个 CPU 缓冲区的内容上传到 GPU 缓冲区，实现 CPU 到 GPU 的数据同步。
- **GetGPUVirtualAddress**: 返回内部 Falcor `Buffer` 对象的 GPU 地址，供 SDK 在其内部渲染管线中直接使用该 GPU 缓冲区。
- **resize**: 同时分配 CPU 向量和 GPU 缓冲区，GPU 缓冲区使用 `Constant | ShaderResource` 绑定标志和 `Upload` 内存类型，以支持 CPU 端写入和 GPU 端读取。
- **临时方案**: 注释中说明该类是一个临时解决方案，当 DDGIVolume 提供更好的接口后可以移除。
