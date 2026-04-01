# NativeHandle 源码文档

> 路径: `Source/Falcor/Core/API/NativeHandle.h`
> 类型: C++ 头文件
> 模块: Core/API

## 功能概述

NativeHandle 是一个类型安全的原生图形 API 句柄包装器，能够存储 D3D12 和 Vulkan 的各种原生句柄（如设备、资源、管线、Fence、命令队列等），并提供类型检查的创建和访问接口。句柄值固定为 64 位。

## 类与接口

### `NativeHandleType`（枚举）
列举所有支持的原生句柄类型：
- D3D12: `ID3D12Device`, `ID3D12Resource`, `ID3D12PipelineState`, `ID3D12Fence`, `ID3D12CommandQueue`, `ID3D12GraphicsCommandList`, `D3D12_CPU_DESCRIPTOR_HANDLE`
- Vulkan: `VkInstance`, `VkPhysicalDevice`, `VkDevice`, `VkImage`, `VkImageView`, `VkBuffer`, `VkBufferView`, `VkPipeline`, `VkFence`, `VkQueue`, `VkCommandBuffer`, `VkSampler`

### `NativeHandle`
- **职责**: 类型安全的原生句柄存储

| 方法 | 说明 |
|------|------|
| `template<T> NativeHandle(T native)` | 从原生句柄构造 |
| `NativeHandleType getType() const` | 获取句柄类型 |
| `bool isValid() const` | 检查是否有效 |
| `template<T> T as() const` | 转换为原生类型（带类型断言） |

## 设计说明

类型信息和转换通过 `NativeHandleTrait<T>` 特化实现，具体特化在 `NativeHandleTraits.h` 中定义。这种分离避免在所有头文件中暴露 D3D12/Vulkan 的重量级头文件。

## 依赖关系
### 本文件引用
- `Core/Error.h`

### 被以下文件引用
- `Device.h`, `Fence.h`, `ComputeStateObject.h`, `LowLevelContextData.h`, `Sampler.h` 等
