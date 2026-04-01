# D3D12DescriptorData 源码文档

> 路径: `Source/Falcor/Core/API/Shared/D3D12DescriptorData.h`
> 类型: C++ 头文件
> 模块: Core/API/Shared

## 功能概述

定义了描述符池和描述符集的平台相关 API 数据结构。这些结构体作为 `D3D12DescriptorPool` 和 `D3D12DescriptorSet` 的内部数据载体，存储底层 D3D12 描述符堆分配信息和范围偏移映射。

## 类与接口

### `DescriptorPoolApiData`
- **职责**: 存储描述符池所管理的所有 D3D12 描述符堆实例。每种 D3D12 描述符堆类型对应一个堆对象。

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `kHeapCount` | `static constexpr size_t` | 堆数量常量，等于 `D3D12_DESCRIPTOR_HEAP_TYPE_NUM_TYPES`（即 4 种堆类型） |
| `pHeaps[kHeapCount]` | `ref<D3D12DescriptorHeap>` | 按 D3D12 描述符堆类型索引的堆数组（CBV_SRV_UAV、SAMPLER、RTV、DSV） |

### `DescriptorSetApiData`
- **职责**: 存储单个描述符集的堆分配数据，包括连续分配块和各范围的基础偏移量。

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `pAllocation` | `D3D12DescriptorHeap::Allocation::SharedPtr` | 从描述符堆中分配的连续描述符块，即使存在多个范围也始终分配为一个连续块 |
| `rangeBaseOffset` | `std::vector<uint32_t>` | 每个范围在分配块中的基础偏移量，用于按范围索引定位描述符 |
| `descriptorCount` | `uint32_t` | 该描述符集中的描述符总数 |

## 依赖关系

### 本文件引用
- `D3D12DescriptorHeap.h` — 使用 `D3D12DescriptorHeap` 及其 `Allocation` 类型

### 被以下文件引用
- `D3D12DescriptorPool.cpp` — 用于构造和管理描述符池的内部数据
- `D3D12DescriptorSet.cpp` — 用于构造和管理描述符集的内部数据

## 实现细节

- `DescriptorPoolApiData` 按 D3D12 的四种描述符堆类型（CBV_SRV_UAV、SAMPLER、RTV、DSV）维护堆数组，并非所有槽位都一定被初始化，仅在对应类型的描述符数量大于 0 时才创建堆。
- `DescriptorSetApiData` 将多个描述符范围合并为一个连续的堆分配，通过 `rangeBaseOffset` 记录每个范围的起始偏移，以支持按范围索引快速访问描述符。
