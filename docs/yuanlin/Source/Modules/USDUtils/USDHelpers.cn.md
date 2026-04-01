# USDHelpers 源码文档

> 路径: `Source/Modules/USDUtils/USDHelpers.h`
> 类型: C++ 头文件（仅头文件）
> 模块: USDUtils

## 功能概述

定义跨平台的 USD 编译警告抑制宏。USD 头文件在 MSVC、Clang、GCC 下会触发各种编译器警告，此文件通过平台相关的 `#pragma` 指令将这些警告暂时禁用。

## 类与接口

（无类定义）

## 宏定义

| 宏 | 说明 |
|----|------|
| `BEGIN_DISABLE_USD_WARNINGS` | 在包含 USD 头文件前调用，压入当前警告状态并禁用特定警告 |
| `END_DISABLE_USD_WARNINGS` | 在包含 USD 头文件后调用，恢复之前的警告状态 |

### 各平台禁用的警告

| 平台 | 禁用的警告 |
|------|-----------|
| MSVC | C4003（宏参数不足）、C4244/C4267（类型截断）、C4305（double 转 float）、C5033（`register` 已弃用） |
| Clang | `-Wignored-attributes` |
| GCC | `-Wignored-attributes`、`-Wparentheses` |

## 依赖关系

### 被以下文件引用

- `USDUtils/USDUtils.h`
- `USDUtils/ConvertedInput.h`
- `USDUtils/ConvertedMaterialCache.h`
- `USDUtils/USDScene1Utils.h`
- `USDUtils/Tessellator/Tessellation.h`
- `PreviewSurfaceConverter/PreviewSurfaceConverter.h`
- `USDImporter/ImporterContext.h`
