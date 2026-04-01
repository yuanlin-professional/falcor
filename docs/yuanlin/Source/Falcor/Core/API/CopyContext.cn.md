# CopyContext 源码文档

> 路径: `Source/Falcor/Core/API/CopyContext.h` / `CopyContext.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

CopyContext 是命令上下文层级结构的基类，提供资源复制、屏障管理、同步以及纹理/缓冲区数据传输功能。它是 ComputeContext 和 RenderContext 的基础。

## 类与接口

### `CopyContext`
- **职责**: 管理命令列表提交、资源屏障、数据复制操作和 CUDA 互操作同步

#### 内部类 `ReadTextureTask`
- **职责**: 异步纹理读取任务，使用 Fence 同步

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `virtual void submit(bool wait = false)` | 提交命令列表 |
| `uint64_t signal(Fence*, uint64_t value)` | 信号 Fence |
| `void wait(Fence*, uint64_t value)` | 设备端等待 Fence |
| `bool resourceBarrier(const Resource*, Resource::State, const ResourceViewInfo*)` | 插入资源屏障 |
| `void uavBarrier(const Resource*)` | 插入 UAV 屏障 |
| `void copyResource(const Resource* pDst, const Resource* pSrc)` | 复制整个资源 |
| `void copySubresource(...)` | 复制子资源 |
| `void copyBufferRegion(...)` | 复制缓冲区区域 |
| `void copySubresourceRegion(...)` | 复制纹理子资源区域 |
| `void updateSubresourceData(...)` | 更新纹理子资源数据 |
| `void updateBuffer(...)` | 更新缓冲区数据 |
| `void readBuffer(...)` | 读取缓冲区数据 |
| `std::vector<uint8_t> readTextureSubresource(...)` | 同步读取纹理子资源 |
| `ReadTextureTask::SharedPtr asyncReadTextureSubresource(...)` | 异步读取纹理子资源 |
| `void waitForCuda(cudaStream_t)` | 等待 CUDA 流完成 |
| `void waitForFalcor(cudaStream_t)` | 等待 Falcor 命令队列完成 |
| `void addAftermathMarker(std::string_view name)` | 添加 Aftermath 调试标记 |

## 实现细节

- `readBuffer` 使用设备的回读堆分配临时内存，执行复制后同步读取
- 资源屏障智能区分纹理的全局屏障和子资源级别屏障
- D3D12 特定：支持自定义 GPU 描述符堆绑定
- CUDA 互操作：D3D12 使用共享 Fence，Vulkan 使用全同步回退

## 依赖关系
### 本文件引用
- `Resource.h`, `ResourceViews.h`, `Buffer.h`, `Texture.h`, `Fence.h`
- `LowLevelContextData.h`, `GFXAPI.h`, `GFXHelpers.h`, `Aftermath.h`

### 被以下文件引用
- `ComputeContext.h`（继承）, `RenderContext.h`（间接继承）
