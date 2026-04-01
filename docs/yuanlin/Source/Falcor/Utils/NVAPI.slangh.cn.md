# NVAPI.slangh 源码文档

> 路径: `Source/Falcor/Utils/NVAPI.slangh`
> 类型: Slang 着色器头文件 (.slangh)
> 模块: Utils

## 功能概述

NVAPI 着色器扩展的 Slang 头文件。通过条件编译引入 NVAPI 着色器扩展枚举定义，并导入 `Utils.NVAPI` 模块以获取完整的 NVAPI 着色器扩展支持。

## 结构体与接口

无自定义结构体或接口。

## 函数

无直接定义的函数。

## 依赖关系

### import

- `nvapi/nvShaderExtnEnums.h`（条件包含，当 `FALCOR_NVAPI_AVAILABLE` 为真）
- `Utils.NVAPI`（Slang 模块导入）

## 实现细节

- 作为 `.slangh` 头文件，用于在其他着色器文件中通过 `#include` 引入
- `NVAPI.slang` 提供实际扩展函数，本文件提供扩展枚举和统一入口
