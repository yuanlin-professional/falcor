# D3D12DescriptorPool 源码文档

> 路径: `Source/Falcor/Core/API/Shared/D3D12DescriptorPool.h` / `D3D12DescriptorPool.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API/Shared

## 功能概述

实现 D3D12 描述符池，负责按着色器资源类型聚合和管理多个 D3D12 描述符堆。描述符池根据 Falcor 定义的着色器资源类型（SRV、UAV、CBV、采样器、RTV、DSV 等）自动映射到对应的 D3D12 描述符堆类型，并提供基于 Fence 的延迟释放机制以确保 GPU 完成使用后再回收描述符。

## 类与接口

### `D3D12DescriptorPool`
- **继承**: `Object`
- **职责**: 管理一组 D3D12 描述符堆，按资源类型分配描述符，并通过 Fence 同步实现延迟释放。

#### 类型别名

| 别名 | 实际类型 | 说明 |
|------|----------|------|
| `ApiHandle` | `ID3D12DescriptorHeapPtr` | D3D12 描述符堆智能指针 |
| `CpuHandle` | `D3D12_CPU_DESCRIPTOR_HANDLE` | CPU 描述符句柄 |
| `GpuHandle` | `D3D12_GPU_DESCRIPTOR_HANDLE` | GPU 描述符句柄 |
| `ApiData` | `DescriptorPoolApiData` | 池的平台相关内部数据 |
| `Type` | `ShaderResourceType` | 着色器资源类型枚举 |

#### 关键常量

| 常量 | 说明 |
|------|------|
| `kTypeCount` | 着色器资源类型总数（13 种） |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<D3D12DescriptorPool> create(Device* pDevice, const Desc& desc, ref<Fence> pFence)` | 创建描述符池，指定各类型描述符数量和 Fence 同步对象 |
| `uint32_t getDescCount(Type type) const` | 获取指定类型的描述符数量 |
| `uint32_t getTotalDescCount() const` | 获取所有类型的描述符总数 |
| `bool isShaderVisible() const` | 查询池是否对着色器可见 |
| `const ApiHandle& getApiHandle(uint32_t heapIndex) const` | 按堆索引获取底层 D3D12 描述符堆句柄 |
| `const ApiData* getApiData() const` | 获取内部平台数据指针 |
| `void executeDeferredReleases()` | 执行延迟释放，回收 GPU 已完成使用的描述符集 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mDesc` | `Desc` | 池的描述信息 |
| `mpApiData` | `std::shared_ptr<ApiData>` | 平台相关数据，包含各类型的描述符堆 |
| `mpFence` | `ref<Fence>` | 用于同步的 Fence 对象 |
| `mpDeferredReleases` | `std::priority_queue<DeferredRelease, ...>` | 延迟释放的优先队列，按 Fence 值排序 |

### `D3D12DescriptorPool::Desc`
- **职责**: 描述符池的配置描述，支持链式调用设置各类型描述符数量和着色器可见性。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `Desc& setDescCount(Type type, uint32_t count)` | 设置指定着色器资源类型的描述符数量 |
| `Desc& setShaderVisible(bool visible)` | 设置池是否对着色器可见 |

### 辅助函数

| 函数签名 | 说明 |
|----------|------|
| `D3D12_DESCRIPTOR_HEAP_TYPE falcorToDxDescType(D3D12DescriptorPool::Type t)` | 将 Falcor 着色器资源类型映射到 D3D12 描述符堆类型 |

## 依赖关系

### 本文件引用
- `D3D12Handles.h` — D3D12 COM 智能指针定义
- `D3D12DescriptorData.h` — 描述符池/集内部数据结构
- `D3D12DescriptorHeap.h` — 描述符堆（通过 `D3D12DescriptorData.h` 间接引用）
- `Core/Macros.h` — 框架宏定义
- `Core/Object.h` — 基类对象
- `Core/API/ShaderResourceType.h` — 着色器资源类型枚举
- `Core/API/Fence.h` — Fence 同步对象
- `Core/API/Device.h` — GPU 设备接口

### 被以下文件引用
- `D3D12DescriptorSet.h` / `D3D12DescriptorSet.cpp` — 描述符集从池中分配描述符

## 实现细节

- **类型映射**: `falcorToDxDescType` 将 Falcor 的 13 种着色器资源类型映射到 D3D12 的 4 种描述符堆类型：纹理/缓冲区 SRV/UAV 和 CBV 映射到 `CBV_SRV_UAV`，DSV 映射到 `DSV`，RTV 映射到 `RTV`，采样器映射到 `SAMPLER`。
- **堆创建**: 构造函数汇总各类型的描述符数量到 4 种 D3D12 堆类型中，仅在数量大于 0 时创建对应的 `D3D12DescriptorHeap`。
- **延迟释放**: 描述符集释放时不立即回收，而是将其与当前 Fence 信号值一起放入优先队列。`executeDeferredReleases` 检查 Fence 当前值，回收所有已完成的释放请求。这确保了 GPU 使用完描述符后才进行回收。
