# GpuMemoryHeap 源码文档

> 路径: `Source/Falcor/Core/API/GpuMemoryHeap.h` / `GpuMemoryHeap.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

GpuMemoryHeap 实现了分页式 GPU 内存堆管理器。它将大块 GPU 内存分成固定大小的页面，支持子分配以减少独立缓冲区创建的开销。主要用于上传堆和回读堆场景中的临时内存分配。

## 类与接口

### `GpuMemoryHeap`
- **继承**: `Object`
- **职责**: 分页式 GPU 内存管理

#### 内部结构体
| 结构体 | 说明 |
|--------|------|
| `BaseData` | 基础分配数据（GFX 缓冲区资源、大小、偏移、映射指针） |
| `Allocation` | 分配结果（继承 BaseData，附加页面 ID 和 Fence 值） |
| `PageData` | 页面数据（继承 BaseData，附加分配计数和当前偏移） |

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `static ref<GpuMemoryHeap> create(ref<Device>, MemoryType, size_t pageSize, ref<Fence>)` | 创建内存堆 |
| `Allocation allocate(size_t size, size_t alignment)` | 分配指定大小的内存 |
| `Allocation allocate(size_t size, ResourceBindFlags)` | 根据绑定标志分配内存 |
| `void release(Allocation& data)` | 释放分配（延迟释放） |
| `void executeDeferredReleases()` | 执行延迟释放 |

## 实现细节

- 超过页面大小的分配创建独立的"超级页面"
- 延迟释放使用优先队列按 Fence 值排序
- 当活动页面空间不足时分配新页面
- 已释放的页面回收到可用页面队列中复用

## 依赖关系
### 本文件引用
- `Resource.h`, `Buffer.h`, `Fence.h`, `Handles.h`

### 被以下文件引用
- `Device.h`（持有上传堆和回读堆实例）
