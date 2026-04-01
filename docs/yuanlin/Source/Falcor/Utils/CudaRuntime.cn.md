# CudaRuntime.h 源码文档

> 路径: `Source/Falcor/Utils/CudaRuntime.h`
> 类型: C++ 头文件（仅头文件）
> 模块: Utils

## 功能概述

CUDA 运行时头文件的包装器。CUDA 运行时在全局命名空间中定义了向量类型（如 `int2`、`float3` 等），这些类型与 Falcor 的 `Falcor::math` 命名空间中的同名类型冲突。本文件通过宏重命名的方式解决此冲突，Falcor 代码应包含此头文件而非直接包含 `<cuda_runtime.h>`。

## 类与接口

无类定义。

## 依赖关系

### 本文件引用

- `<cuda_runtime.h>` — CUDA 运行时

### 被以下文件引用

- `CudaUtils.h` — 通过此文件间接引入 CUDA 运行时

## 实现细节

- 在包含 `<cuda_runtime.h>` 之前，将 `int1`-`int4`、`uint1`-`uint4`、`float1`-`float4` 重定义为 `cuda_int1` 等
- 包含 CUDA 头文件后立即 `#undef` 所有重定义宏
- 这确保 CUDA 的向量类型在 Falcor 代码中不可见，避免命名冲突
