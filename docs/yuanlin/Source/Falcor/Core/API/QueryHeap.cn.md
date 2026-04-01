# QueryHeap 源码文档

> 路径: `Source/Falcor/Core/API/QueryHeap.h` / `QueryHeap.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

QueryHeap 封装了 GPU 查询池，支持时间戳查询、遮挡查询和管线统计查询。提供查询索引的分配和释放管理。

## 类与接口

### `QueryHeap`
- **继承**: `Object`
- **职责**: 管理 GPU 查询池

#### 内部枚举 `Type`
| 值 | 说明 |
|----|------|
| `Timestamp` | 时间戳查询 |
| `Occlusion` | 遮挡查询 |
| `PipelineStats` | 管线统计查询 |

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `static ref<QueryHeap> create(ref<Device>, Type, uint32_t count)` | 创建查询堆 |
| `uint32_t allocate()` | 分配一个查询索引 |
| `void release(uint32_t entry)` | 释放查询索引 |
| `gfx::IQueryPool* getGfxQueryPool() const` | 获取 GFX 查询池 |

## 实现细节

- 使用 `deque` 管理已释放的查询索引，优先复用
- 当所有索引用尽时返回 `kInvalidIndex`

## 依赖关系
### 本文件引用
- `Handles.h`, `Core/Macros.h`, `Core/Error.h`, `Core/Object.h`

### 被以下文件引用
- `Device.h`（持有时间戳查询堆）, `GpuTimer.h`
