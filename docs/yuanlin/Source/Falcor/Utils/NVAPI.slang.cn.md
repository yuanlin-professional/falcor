# NVAPI.slang 源码文档

> 路径: `Source/Falcor/Utils/NVAPI.slang`
> 类型: Slang 着色器文件
> 模块: Utils

## 功能概述

NVAPI 着色器扩展的 Slang 包装文件。当 `FALCOR_NVAPI_AVAILABLE` 宏定义时，包含 NVAPI 的 HLSL 扩展头文件，提供 NVIDIA GPU 特有的着色器扩展功能。

## 结构体与接口

无自定义结构体或接口。

## 函数

通过条件包含 `nvapi/nvHLSLExtns.h` 提供 NVAPI 扩展函数。

## 依赖关系

### import

- `nvapi/nvHLSLExtns.h`（条件包含，当 `FALCOR_NVAPI_AVAILABLE` 为真）

## 实现细节

- 仅在 NVAPI 可用时才引入扩展头文件
- 提供诸如原子操作扩展、着色器执行重排等 NVIDIA 专有着色器功能
