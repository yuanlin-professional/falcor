# GpuTimer 源码文档

> 路径: `Source/Falcor/Core/API/GpuTimer.h` / `GpuTimer.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

GpuTimer 封装了 GPU 时间戳查询，提供 `begin()`/`end()` 配对调用来测量 GPU 执行时间（毫秒）。

## 类与接口

### `GpuTimer`
- **继承**: `Object`
- **职责**: GPU 时间测量

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `static ref<GpuTimer> create(ref<Device>)` | 创建计时器 |
| `void begin()` | 开始计时 |
| `void end()` | 结束计时 |
| `void resolve()` | 解析时间戳（begin/end 之后调用） |
| `double getElapsedTime()` | 获取经过的毫秒数（resolve 之后调用） |

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `mStart` / `mEnd` | `uint32_t` | 查询堆中的起止索引 |
| `mpResolveBuffer` | `ref<Buffer>` | GPU 端解析缓冲区 |
| `mpResolveStagingBuffer` | `ref<Buffer>` | CPU 可读回读缓冲区 |

## 依赖关系
### 本文件引用
- `QueryHeap.h`, `Buffer.h`, `Core/Object.h`

### 被以下文件引用
- 性能分析相关模块
