# NativeHandleTraits 源码文档

> 路径: `Source/Falcor/Core/API/NativeHandleTraits.h`
> 类型: C++ 头文件
> 模块: Core/API

## 功能概述

NativeHandleTraits.h 为 `NativeHandle` 提供所有支持的原生类型的特化 trait，实现原生句柄与 64 位值之间的打包/解包。此文件包含 D3D12 和 Vulkan 的头文件，仅在需要实际创建或访问 NativeHandle 时包含。

## 实现机制

使用 `FALCOR_NATIVE_HANDLE(T, TYPE)` 宏为每种原生类型生成 `NativeHandleTrait<T>` 特化，提供：
- `static const NativeHandleType type` — 类型标识
- `static uint64_t pack(T native)` — 使用 `fstd::bit_cast` 打包
- `static T unpack(uint64_t value)` — 使用 `fstd::bit_cast` 解包

### D3D12 类型
`ID3D12Device*`, `ID3D12Resource*`, `ID3D12PipelineState*`, `ID3D12Fence*`, `ID3D12CommandQueue*`, `ID3D12GraphicsCommandList*`, `D3D12_CPU_DESCRIPTOR_HANDLE`

### Vulkan 类型
`VkInstance`, `VkPhysicalDevice`, `VkDevice`, `VkImage`, `VkImageView`, `VkBuffer`, `VkBufferView`, `VkPipeline`, `VkFence`, `VkQueue`, `VkCommandBuffer`, `VkSampler`

## 依赖关系
### 本文件引用
- `NativeHandle.h`, D3D12 头文件, Vulkan 头文件, `fstd/bit.h`

### 被以下文件引用
- 需要创建或解引用 NativeHandle 的 .cpp 文件
