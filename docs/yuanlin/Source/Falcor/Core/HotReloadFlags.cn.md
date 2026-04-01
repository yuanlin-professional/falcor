# HotReloadFlags 源码文档

> 路径: `Source/Falcor/Core/HotReloadFlags.h`
> 类型: C++ 头文件（仅头文件）
> 模块: Core

## 功能概述

定义热重载标志枚举，用于标识哪些可热重载资源发生了变更。目前仅支持着色器程序的热重载。

## 枚举类型

### `HotReloadFlags`
位标志枚举，通过 `FALCOR_ENUM_CLASS_OPERATORS` 宏启用位运算操作符。

| 值 | 说明 |
|------|------|
| `None` | 无变更 |
| `Program` | 着色器程序已重载 |

## 依赖关系

### 本文件引用
- `Core/Macros.h` — `FALCOR_ENUM_CLASS_OPERATORS` 宏

### 被以下文件引用
- `Core/SampleApp.h` — 热重载回调参数类型
