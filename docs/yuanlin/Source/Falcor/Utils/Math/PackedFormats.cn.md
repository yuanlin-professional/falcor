# PackedFormats 源码文档

> 路径: `Source/Falcor/Utils/Math/PackedFormats.h`
> 类型: C++ 头文件
> 模块: Utils/Math

## 功能概述

提供 CPU 端的格式打包工具函数，主要用于法线的八面体映射编码/解码。支持 2x16 位 snorm 格式的法线压缩。

## 类与接口

无类定义，仅包含内联函数。

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `float2 oct_wrap(float2 v)` | 八面体映射辅助折叠函数 |
| `float2 ndir_to_oct_snorm(float3 n)` | 方向转八面体映射（有符号归一化） |
| `float3 oct_to_ndir_snorm(float2 p)` | 八面体映射转方向 |
| `uint32_t encodeNormal2x16(float3 normal)` | 法线编码为 2x16 位 snorm |
| `float3 decodeNormal2x16(uint32_t packedNormal)` | 从 2x16 位 snorm 解码法线 |

## 依赖关系
### 本文件引用
- `Vector.h`
- `FormatConversion.h`
