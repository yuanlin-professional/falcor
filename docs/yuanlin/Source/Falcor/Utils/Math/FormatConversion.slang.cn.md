# FormatConversion.slang 着色器文档

> 路径: `Source/Falcor/Utils/Math/FormatConversion.slang`
> 类型: Slang 着色器
> 模块: Utils/Math

## 功能概述

提供 GPU 端的数据格式打包与解包工具函数集合，覆盖 8 位/16 位的 snorm、unorm 格式，以及 R11G11B10 HDR 格式和 64 位整数转换。所有函数兼容 DXGI 格式规范。

## 函数

### 8 位 snorm
| 函数签名 | 说明 |
|----------|------|
| `int floatToSnorm8(float v)` | float 转 8 位 snorm |
| `float unpackSnorm8(uint packed)` | 解包 8 位 snorm |
| `uint packSnorm8(float v)` | 打包 8 位 snorm |
| `float2 unpackSnorm2x8(uint packed)` | 解包两个 8 位 snorm |
| `uint packSnorm2x8(float2 v)` | 打包两个 8 位 snorm |

### 8 位 unorm
| 函数签名 | 说明 |
|----------|------|
| `uint packUnorm8(float v)` / `packUnorm8_unsafe(float v)` | 打包 8 位 unorm |
| `float unpackUnorm8(uint packed)` | 解包 8 位 unorm |
| `uint packUnorm2x8(float2 v)` / `packUnorm3x8(float3 v)` / `packUnorm4x8(float4 v)` | 多通道打包 |
| `float2 unpackUnorm2x8(uint)` / `float3 unpackUnorm3x8(uint)` / `float4 unpackUnorm4x8(uint)` | 多通道解包 |

### 16 位 snorm / unorm
| 函数签名 | 说明 |
|----------|------|
| `int floatToSnorm16(float v)` | float 转 16 位 snorm |
| `float unpackSnorm16(uint packed)` | 解包 16 位 snorm |
| `uint packSnorm2x16(float2 v)` | 打包两个 16 位 snorm |
| `uint packUnorm16(float v)` | 打包 16 位 unorm |
| `float2 unpackUnorm2x16(uint packed)` | 解包两个 16 位 unorm |

### HDR 格式
| 函数签名 | 说明 |
|----------|------|
| `uint packR11G11B10(float3 v)` | 打包 R11G11B10 HDR 颜色 |
| `float3 unpackR11G11B10(uint packed)` | 解包 R11G11B10 HDR 颜色 |

### 64 位整数
| 函数签名 | 说明 |
|----------|------|
| `uint64_t u2x32to64(uint2 v)` | 两个 32 位整数编码为 64 位 |
| `uint2 u64to2x32(uint64_t v)` | 64 位整数解码为两个 32 位 |

## 依赖关系
### import
- 无外部依赖
