# D3D12ConstantBufferView 源码文档

> 路径: `Source/Falcor/Core/API/Shared/D3D12ConstantBufferView.h` / `D3D12ConstantBufferView.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API/Shared

## 功能概述

提供 D3D12 原生常量缓冲区视图 (CBV) 的封装实现。由于 GFX 抽象层不支持常量缓冲区视图，本文件为希望直接使用原生 D3D12 描述符集 API 的应用程序提供了一个基于原始 D3D12 的 CBV 实现。该类通过内部持有一个 `D3D12DescriptorSet` 来管理底层的 D3D12 CPU 描述符句柄。

## 类与接口

### `D3D12ConstantBufferView`
- **继承**: `Object`
- **职责**: 封装 D3D12 常量缓冲区视图，提供从 GPU 地址、缓冲区对象或空视图创建 CBV 的能力，并允许获取对应的 D3D12 CPU 描述符句柄。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<D3D12ConstantBufferView> create(ref<Device> pDevice, uint64_t gpuAddress, uint32_t byteSize)` | 通过指定 GPU 地址和字节大小创建 CBV |
| `static ref<D3D12ConstantBufferView> create(ref<Device> pDevice, ref<Buffer> pBuffer)` | 从现有缓冲区对象创建 CBV，自动获取 GPU 地址和大小 |
| `static ref<D3D12ConstantBufferView> create(ref<Device> pDevice)` | 创建一个空的 CBV（null view） |
| `D3D12_CPU_DESCRIPTOR_HANDLE getD3D12CpuHeapHandle() const` | 获取该资源视图对应的 D3D12 CPU 描述符句柄 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpBuffer` | `ref<Buffer>` | 关联的缓冲区对象（可为空） |
| `mpDescriptorSet` | `ref<D3D12DescriptorSet>` | 内部持有的描述符集，用于存储 CBV 描述符 |

### 辅助函数

| 函数签名 | 说明 |
|----------|------|
| `ref<D3D12DescriptorSet> createCbvDescriptor(ref<Device> pDevice, const D3D12_CONSTANT_BUFFER_VIEW_DESC& desc)` | 内部辅助函数，创建包含单个 CBV 的描述符集，通过 D3D12 设备的 `CreateConstantBufferView` 写入描述符 |

## 依赖关系

### 本文件引用
- `D3D12DescriptorSet.h` — 用于描述符集的分配和管理
- `Core/API/Buffer.h` — 缓冲区资源抽象
- `Core/API/Device.h` — GPU 设备接口
- `Core/API/NativeHandleTraits.h` — 原生句柄转换
- `Core/Macros.h` — 框架宏定义
- `Core/Object.h` — 基类对象

### 被以下文件引用
- `D3D12DescriptorSet.cpp` — 在 `setCbv` 方法中使用

## 实现细节

- 所有三个 `create` 重载均会调用 `pDevice->requireD3D12()` 确保当前后端为 D3D12。
- 内部通过 `createCbvDescriptor` 辅助函数创建一个包含单个 CBV 类型范围的 `D3D12DescriptorSetLayout`，然后分配一个 `D3D12DescriptorSet`，并调用 `ID3D12Device::CreateConstantBufferView` 将 CBV 描述符写入分配的 CPU 描述符堆位置。
- 使用 CPU 描述符池 (`getD3D12CpuDescriptorPool`) 分配描述符，因此该视图仅用于 CPU 端操作。
