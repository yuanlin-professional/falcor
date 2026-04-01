# BufferAllocator 源码文档

> 路径: `Source/Falcor/Utils/BufferAllocator.h` + `Source/Falcor/Utils/BufferAllocator.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils

## 功能概述

GPU 缓冲区内存管理工具类。在 CPU 端维护一个动态大小的后备缓冲区，支持内存分配和更新操作。GPU 缓冲区按需懒创建和更新。支持结构化缓冲区和原始缓冲区两种模式，并提供最小字节对齐和缓存行对齐约束。

## 类与接口

### `BufferAllocator`

- **继承**: 无
- **职责**: 管理 CPU/GPU 缓冲区的分配、数据写入和脏区域追踪，确保 GPU 缓冲区在访问时自动同步

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `BufferAllocator(size_t alignment, size_t elementSize, size_t cacheLineSize, ResourceBindFlags bindFlags)` | 构造函数，指定对齐、元素大小、缓存行大小和绑定标志 |
| `size_t allocate(size_t byteSize)` | 分配指定字节数的内存，返回偏移量 |
| `size_t allocate<T>(size_t count)` | 分配指定类型数组的内存 |
| `size_t pushBack(const T& obj)` | 分配并拷贝对象，返回偏移量 |
| `size_t emplaceBack(Args&&... args)` | 分配并原地构造对象 |
| `void setBlob(const void* pData, size_t byteOffset, size_t byteSize)` | 向指定内存区域写入数据 |
| `void set<T>(size_t byteOffset, const T& obj)` | 设置指定偏移处的对象 |
| `const T& get<T>(size_t byteOffset) const` | 获取指定偏移处的对象 |
| `void modified(size_t byteOffset, size_t byteSize)` | 标记内存区域为已修改 |
| `void clear()` | 清空所有分配 |
| `ref<Buffer> getGPUBuffer(ref<Device> pDevice)` | 获取 GPU 缓冲区，自动同步脏数据 |
| `uint8_t* getStartPointer()` | 获取 CPU 缓冲区起始指针 |
| `size_t getSize() const` | 获取缓冲区大小 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mAlignment` | `const size_t` | 最小对齐要求 |
| `mElementSize` | `const size_t` | 结构化缓冲区的元素大小（0 表示原始缓冲区） |
| `mCacheLineSize` | `const size_t` | 缓存行大小 |
| `mBindFlags` | `const ResourceBindFlags` | GPU 缓冲区绑定标志 |
| `mDirty` | `Range` | 脏数据范围 |
| `mBuffer` | `std::vector<uint8_t>` | CPU 端后备缓冲区 |
| `mpGpuBuffer` | `ref<Buffer>` | GPU 缓冲区 |

## 依赖关系

### 本文件引用

- `Core/Macros.h` — 宏定义
- `Core/API/Buffer.h` — GPU 缓冲区类
- `Core/API/Device.h` — 设备接口
- `Utils/Math/Common.h` — `isPowerOf2`、`align_to` 等数学工具

### 被以下文件引用

- 场景数据管理、材质系统等需要动态 GPU 缓冲区管理的模块

## 实现细节

- 使用单一脏区域（`Range`）追踪 CPU 到 GPU 的数据同步范围
- GPU 缓冲区在首次访问或大小不足时自动创建/重建
- `elementSize > 0` 时创建结构化缓冲区，否则创建原始缓冲区
- 对齐和缓存行约束在 `computeAndAllocatePadding` 中处理
