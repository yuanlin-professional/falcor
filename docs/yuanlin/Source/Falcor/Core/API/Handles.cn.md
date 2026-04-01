# Handles 源码文档

> 路径: `Source/Falcor/Core/API/Handles.h`
> 类型: C++ 头文件
> 模块: Core/API

## 功能概述

Handles.h 定义了跨平台的基础 GPU 句柄类型别名，以及引入 Slang 的 COM 指针和 GFX 接口头文件。

## 类型定义

| 类型别名 | 说明 |
|----------|------|
| `GpuAddress` | `uint64_t`，GPU 地址 |
| `SharedResourceApiHandle` | `void*`，共享资源句柄（Windows: HANDLE） |
| `SharedFenceApiHandle` | `void*`，共享 Fence 句柄 |

## 引入的头文件
- `slang.h`, `slang-gfx.h`, `slang-com-ptr.h`
- `Core/Platform/PlatformHandles.h`

## 依赖关系
### 被以下文件引用
- 几乎所有 Core/API 头文件都包含 Handles.h
