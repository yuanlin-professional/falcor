# Fence 源码文档

> 路径: `Source/Falcor/Core/API/Fence.h` / `Fence.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

Fence 类封装了 GPU 同步屏障，用于主机与设备之间的执行同步。Fence 在设备端表示为 64 位整数，每次信号时默认单调递增。支持共享 Fence（用于跨适配器或 CUDA 互操作）。

## 类与接口

### `FenceDesc`（结构体）
| 成员 | 说明 |
|------|------|
| `initialValue` | 初始值 |
| `shared` | 是否为共享 Fence |

### `Fence`
- **继承**: `Object`
- **职责**: GPU-CPU 同步

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `uint64_t signal(uint64_t value = kAuto)` | 从主机端信号 Fence |
| `void wait(uint64_t value = kAuto, uint64_t timeoutNs = kTimeoutInfinite)` | 主机端阻塞等待 |
| `uint64_t getCurrentValue()` | 获取设备端当前值 |
| `uint64_t getSignaledValue() const` | 获取最后信号的值 |
| `uint64_t updateSignaledValue(uint64_t value = kAuto)` | 更新/递增信号值 |
| `NativeHandle getNativeHandle() const` | 获取原生 API 句柄 |
| `SharedResourceApiHandle getSharedApiHandle() const` | 创建共享句柄 |

## 依赖关系
### 本文件引用
- `Handles.h`, `NativeHandle.h`, `Core/Object.h`

### 被以下文件引用
- `CopyContext.h`, `LowLevelContextData.h`, `FencedPool.h`, `GpuMemoryHeap.h`, `Device.h`
