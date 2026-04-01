# Macros 源码文档

> 路径: `Source/Falcor/Core/Macros.h`
> 类型: C++ 头文件（仅头文件）
> 模块: Core

## 功能概述

定义 Falcor 框架的基础预处理宏，包括编译器检测、平台检测、DLL 导入/导出、D3D12 Agility SDK 支持、强制内联、字符串化工具以及枚举位运算操作符生成宏。这是框架中最底层的头文件之一，被几乎所有其他文件引用。

## 编译器检测

| 宏 | 说明 |
|------|------|
| `FALCOR_COMPILER_MSVC` | 值为 1，代表 MSVC 编译器 |
| `FALCOR_COMPILER_CLANG` | 值为 2，代表 Clang 编译器 |
| `FALCOR_COMPILER_GCC` | 值为 3，代表 GCC 编译器 |
| `FALCOR_MSVC` | 当前是否为 MSVC（布尔值） |
| `FALCOR_CLANG` | 当前是否为 Clang（布尔值） |
| `FALCOR_GCC` | 当前是否为 GCC（布尔值） |

## 平台检测

| 宏 | 说明 |
|------|------|
| `FALCOR_PLATFORM_WINDOWS` | 值为 1，代表 Windows 平台 |
| `FALCOR_PLATFORM_LINUX` | 值为 2，代表 Linux 平台 |
| `FALCOR_WINDOWS` | 当前是否为 Windows（布尔值） |
| `FALCOR_LINUX` | 当前是否为 Linux（布尔值） |

## DLL 导入/导出

| 宏 | 说明 |
|------|------|
| `FALCOR_API_EXPORT` | DLL 导出声明（Windows: `__declspec(dllexport)`，Linux: `__attribute__((visibility("default")))`) |
| `FALCOR_API_IMPORT` | DLL 导入声明（Windows: `__declspec(dllimport)`） |
| `FALCOR_API` | 根据 `FALCOR_DLL` 定义自动选择导出或导入 |

## D3D12 Agility SDK

| 宏 | 说明 |
|------|------|
| `FALCOR_D3D12_AGILITY_SDK_VERSION` | Agility SDK 版本号（4） |
| `FALCOR_D3D12_AGILITY_SDK_PATH` | Agility SDK 路径 |
| `FALCOR_EXPORT_D3D12_AGILITY_SDK` | 导出 SDK 版本符号的宏，需在可执行文件主源文件中使用 |

## 工具宏

| 宏 | 说明 |
|------|------|
| `FALCOR_NVAPI_AVAILABLE` | NVAPI 是否可用 |
| `FALCOR_FORCEINLINE` | 跨编译器的强制内联声明 |
| `FALCOR_STRINGIZE(a)` | 预处理器字符串化 |
| `FALCOR_CONCAT_STRINGS(a, b)` | 预处理器标识符拼接 |
| `FALCOR_ENUM_CLASS_OPERATORS(e_)` | 为枚举类生成位运算操作符（`&`、`|`、`~`、`|=`、`&=`、`is_set`、`flip_bit`） |

## 依赖关系

### 本文件引用
- 无外部依赖（纯预处理宏）

### 被以下文件引用
- 框架中几乎所有源文件
