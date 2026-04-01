# ParallelReductionType.slangh 源码文档

> 路径: `Source/Falcor/Utils/Algorithm/ParallelReductionType.slangh`
> 类型: 着色器头文件 (Slang Header)
> 模块: Utils/Algorithm

## 功能概述

定义了并行归约操作中使用的类型常量宏，在宿主端（C++）和设备端（着色器）之间共享。包含纹理格式类型和归约操作类型的枚举定义。

## 结构体与接口

### 格式类型常量

| 宏定义 | 值 | 说明 |
|--------|-----|------|
| `FORMAT_TYPE_UNKNOWN` | 0 | 未知格式类型 |
| `FORMAT_TYPE_FLOAT` | 1 | 浮点格式（包括 float、snorm、unorm） |
| `FORMAT_TYPE_SINT` | 2 | 有符号整数格式 |
| `FORMAT_TYPE_UINT` | 3 | 无符号整数格式 |

### 归约类型常量

| 宏定义 | 值 | 说明 |
|--------|-----|------|
| `REDUCTION_TYPE_UNKNOWN` | 0 | 未知归约类型 |
| `REDUCTION_TYPE_SUM` | 1 | 求和归约 |
| `REDUCTION_TYPE_MINMAX` | 2 | 最小/最大值归约 |

## 函数

无函数定义。本文件仅包含宏常量定义。

## 依赖关系

### import

无外部依赖。

### 被以下文件引用

- `Utils/Algorithm/ParallelReduction.cs.slang` — GPU 端计算着色器
- `Utils/Algorithm/ParallelReduction.cpp` — C++ 宿主端实现

## 实现细节

- 使用 `#define` 预处理宏而非枚举，以确保在 C++ 和 Slang 着色器代码中均可使用
- 该文件通过 `#pragma once` 防止重复包含
