# D3D12DescriptorHeap 源码文档

> 路径: `Source/Falcor/Core/API/Shared/D3D12DescriptorHeap.h` / `D3D12DescriptorHeap.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API/Shared

## 功能概述

封装 D3D12 描述符堆（Descriptor Heap）的管理，提供基于分块（Chunk）的描述符分配和回收机制。该类将描述符堆划分为固定大小的块（每块 64 个描述符），支持高效的描述符分配、释放和复用，是 Falcor 描述符管理系统的底层核心。

## 类与接口

### `D3D12DescriptorHeap`
- **继承**: `Object`
- **职责**: 管理一个 D3D12 描述符堆，提供分块式描述符分配策略，支持标准大小和大块分配，并维护空闲列表以实现高效回收。

#### 类型别名

| 别名 | 实际类型 | 说明 |
|------|----------|------|
| `ApiHandle` | `ID3D12DescriptorHeapPtr` | D3D12 描述符堆的 COM 智能指针 |
| `CpuHandle` | `D3D12_CPU_DESCRIPTOR_HANDLE` | CPU 端描述符句柄 |
| `GpuHandle` | `D3D12_GPU_DESCRIPTOR_HANDLE` | GPU 端描述符句柄 |

#### 关键常量

| 常量 | 值 | 说明 |
|------|-----|------|
| `kDescPerChunk` | `64` | 每个标准分块包含的描述符数量 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<D3D12DescriptorHeap> create(Device* pDevice, D3D12_DESCRIPTOR_HEAP_TYPE type, uint32_t descCount, bool shaderVisible)` | 创建描述符堆，指定类型、数量和着色器可见性 |
| `Allocation::SharedPtr allocateDescriptors(uint32_t count)` | 从堆中分配指定数量的连续描述符 |
| `CpuHandle getBaseCpuHandle() const` | 获取堆起始 CPU 句柄 |
| `GpuHandle getBaseGpuHandle() const` | 获取堆起始 GPU 句柄（仅着色器可见堆有效） |
| `const ApiHandle& getApiHandle() const` | 获取底层 D3D12 描述符堆句柄 |
| `D3D12_DESCRIPTOR_HEAP_TYPE getType() const` | 获取描述符堆类型 |
| `uint32_t getReservedChunkCount() const` | 获取堆中预留的分块总数 |
| `uint32_t getDescriptorSize() const` | 获取单个描述符的字节增量大小 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mCpuHeapStart` | `CpuHandle` | 堆的 CPU 起始句柄 |
| `mGpuHeapStart` | `GpuHandle` | 堆的 GPU 起始句柄（仅着色器可见时有效） |
| `mDescriptorSize` | `uint32_t` | 描述符增量大小 |
| `mMaxChunkCount` | `uint32_t` | 最大分块数 |
| `mAllocatedChunks` | `uint32_t` | 已分配的分块数 |
| `mApiHandle` | `ApiHandle` | D3D12 描述符堆原生句柄 |
| `mFreeChunks` | `std::vector<Chunk::SharedPtr>` | 标准大小空闲分块列表 |
| `mFreeLargeChunks` | `std::multiset<Chunk::SharedPtr, ChunkComparator>` | 大块空闲列表（按块数排序） |

### `D3D12DescriptorHeap::Allocation`
- **职责**: 表示从描述符堆中分配的一段连续描述符区域，提供按索引获取 CPU/GPU 句柄的能力。析构时自动释放关联的分块。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `uint32_t getHeapEntryIndex(uint32_t index) const` | 将相对索引转换为堆内绝对索引 |
| `CpuHandle getCpuHandle(uint32_t index) const` | 获取分配区域中指定相对索引的 CPU 句柄 |
| `GpuHandle getGpuHandle(uint32_t index) const` | 获取分配区域中指定相对索引的 GPU 句柄 |
| `D3D12DescriptorHeap* getHeap()` | 获取所属的描述符堆 |

### `D3D12DescriptorHeap::Chunk`（私有内部结构）
- **职责**: 表示描述符堆中的一个分块单元，跟踪分块的索引、大小、分配引用计数和当前描述符游标。

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `chunkIndex` | `uint32_t` | 分块在堆中的起始分块索引 |
| `chunkCount` | `uint32_t` | 该分块包含的连续标准块数量 |
| `allocCount` | `uint32_t` | 当前分块上的活跃分配计数 |
| `currentDesc` | `uint32_t` | 分块内的当前描述符偏移游标 |

## 依赖关系

### 本文件引用
- `D3D12Handles.h` — D3D12 COM 智能指针定义
- `Core/Error.h` — 错误处理
- `Core/Object.h` — 基类对象
- `Core/API/fwd.h` — 前向声明
- `Core/API/Device.h` — GPU 设备接口
- `Core/API/NativeHandleTraits.h` — 原生句柄转换

### 被以下文件引用
- `D3D12DescriptorData.h` — 使用 `D3D12DescriptorHeap` 及 `Allocation` 类型
- `D3D12DescriptorPool.cpp` — 创建和管理描述符堆实例

## 实现细节

- **分块分配策略**: 描述符堆被逻辑划分为多个分块，每块包含 `kDescPerChunk`（64）个描述符。分配请求首先检查当前分块是否有足够空间，不足时从空闲列表中查找合适的分块，最后才从堆中新分配分块。
- **大块支持**: 当分配请求超过一个标准块的容量时，会分配多个连续的分块。大块和标准块分别维护独立的空闲列表，大块空闲列表使用 `std::multiset` 按块数排序以支持最佳适配查找。
- **引用计数回收**: 每个分块维护 `allocCount` 引用计数，当所有分配释放后（`allocCount == 0`），分块被重置并归还到对应的空闲列表。
- **句柄计算**: CPU 和 GPU 句柄通过基础句柄加上索引乘以描述符增量大小计算得到。GPU 句柄仅在着色器可见堆上有效。
