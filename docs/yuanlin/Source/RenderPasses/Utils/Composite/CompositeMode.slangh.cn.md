# CompositeMode.slangh 源码文档

> 路径: `Source/RenderPasses/Utils/Composite/CompositeMode.slangh`
> 类型: Slang 共享头文件（主机/设备共享）
> 模块: RenderPasses/Utils/Composite

## 功能概述

此文件定义了 Composite 渲染通道在 CPU 和 GPU 之间共享的宏常量，用于指定混合模式和输出格式。

## 结构体与接口

### 混合模式常量

| 宏 | 值 | 说明 |
|----|-----|------|
| `COMPOSITE_MODE_ADD` | 0 | 加法混合 |
| `COMPOSITE_MODE_MULTIPLY` | 1 | 乘法混合 |

### 输出格式常量

| 宏 | 值 | 说明 |
|----|-----|------|
| `OUTPUT_FORMAT_FLOAT` | 0 | 浮点格式 |
| `OUTPUT_FORMAT_UINT` | 1 | 无符号整数格式 |
| `OUTPUT_FORMAT_SINT` | 2 | 有符号整数格式 |

## 依赖关系 / import

- 无外部依赖

## 实现细节

使用 `#pragma once` 防止重复包含。常量值通过 `#define` 定义，在 C++ 端和 Slang 着色器端均可直接使用。
