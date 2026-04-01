# FormatConversion 源码文档

> 路径: `Source/Falcor/Utils/Math/FormatConversion.h`
> 类型: C++ 头文件
> 模块: Utils/Math

## 功能概述

提供 CPU 端的 16 位 snorm（有符号归一化整数）格式转换工具函数，用于法线压缩等场景。对应的 GPU 端实现在 `FormatConversion.slang`。

## 类与接口

无类定义，仅包含内联函数。

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `int floatToSnorm16(float v)` | float 转 16 位 snorm |
| `float unpackSnorm16(uint packed)` | 解包单个 16 位 snorm |
| `uint packSnorm16(float v)` | 打包单个 16 位 snorm |
| `float2 unpackSnorm2x16(uint packed)` | 从一个 dword 解包两个 16 位 snorm |
| `uint packSnorm2x16(float2 v)` | 将两个 float 打包为两个 16 位 snorm |

## 依赖关系
### 本文件引用
- `ScalarMath.h`
- `Vector.h`
