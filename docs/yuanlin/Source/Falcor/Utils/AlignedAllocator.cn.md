# AlignedAllocator.h 源码文档

> 路径: `Source/Falcor/Utils/AlignedAllocator.h`
> 类型: C++ 头文件（仅头文件）
> 模块: Utils

## 功能概述

提供 GPU 对齐内存分配工具类。`AlignedAllocator` 能够满足多种对齐需求，包括最小字节对齐以及（可选地）确保分配的对象不跨越两条缓存行。该类主要用于管理 GPU 端的分配，因此假设基地址起始于缓存行边界，不在 CPU 端提供对齐保证。

## 类与接口

### `AlignedAllocator`

- **继承**: 无
- **职责**: 在内部 `std::vector<uint8_t>` 缓冲区上执行对齐内存分配，支持最小对齐约束和缓存行对齐约束。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `void setMinimumAlignment(int minAlignment)` | 设置分配对象的最小对齐要求，0 表示不执行额外对齐 |
| `void setCacheLineSize(int cacheLineSize)` | 设置缓存行大小，避免对象跨越多条缓存行，0 表示不关心缓存行对齐 |
| `T* allocate(Args&&... args)` | 分配指定类型的对象并执行构造函数，返回指针 |
| `T* allocateSized(size_t size, Args&&... args)` | 分配指定大小的内存（可大于 `sizeof(T)`），并执行构造函数 |
| `void reserve(size_t size)` | 预留缓冲区容量 |
| `void resize(size_t size)` | 调整缓冲区大小 |
| `void* getStartPointer()` | 返回缓冲区起始指针 |
| `size_t offsetOf(void* ptr) const` | 计算给定指针在缓冲区中的偏移量 |
| `void reset()` | 清空缓冲区 |
| `size_t getSize() const` | 返回缓冲区当前大小 |
| `size_t getCapacity() const` | 返回缓冲区容量 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mMinAlignment` | `size_t` | 最小对齐要求，默认 16 字节 |
| `mCacheLineSize` | `size_t` | 缓存行大小，默认 128 字节 |
| `mBuffer` | `std::vector<uint8_t>` | 内部字节缓冲区 |

## 依赖关系

### 本文件引用

- `Core/Error.h` — 断言宏
- `Utils/Math/Common.h` — `isPowerOf2` 等数学工具

### 被以下文件引用

- 场景构建等需要 GPU 对齐内存分配的模块

## 实现细节

- 使用 placement new 在缓冲区内构造对象
- `computeAndAllocatePadding` 方法在分配前计算必要的填充，确保对齐和缓存行约束
- 当对象可放入单条缓存行但当前偏移会导致跨越时，自动移至下一缓存行起始位置
