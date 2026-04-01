# PackedFormats.slang 着色器文档

> 路径: `Source/Falcor/Utils/Math/PackedFormats.slang`
> 类型: Slang 着色器
> 模块: Utils/Math

## 功能概述

GPU 端的高级格式打包工具，包括多种精度的法线编码/解码、3D 索引展平/还原，以及 LogLuv HDR 颜色编码/解码。

## 函数

### 法线编码/解码
| 函数签名 | 说明 |
|----------|------|
| `uint encodeNormal2x8(float3 normal)` | 法线编码为 2x8 位 snorm（八面体映射） |
| `float3 decodeNormal2x8(uint packedNormal)` | 从 2x8 位 snorm 解码法线 |
| `uint encodeNormal2x16(float3 normal)` | 法线编码为 2x16 位 snorm |
| `float3 decodeNormal2x16(uint packedNormal)` | 从 2x16 位 snorm 解码法线 |
| `uint2 encodeNormal3x16(float3 normal)` | 法线编码为 3x16 位 snorm |
| `float3 decodeNormal3x16(uint2 packedNormal)` | 从 3x16 位 snorm 解码法线 |

### 索引操作
| 函数签名 | 说明 |
|----------|------|
| `uint flatten3D(uint3 idx, uint width, uint height)` | 3D 索引展平为 1D |
| `uint3 unflatten3D(uint flattenedIdx, uint width, uint height)` | 1D 索引还原为 3D |

### HDR 颜色编码
| 函数签名 | 说明 |
|----------|------|
| `uint encodeLogLuvHDR(float3 color)` | RGB 编码为 32 位 LogLuv HDR 格式 |
| `float3 decodeLogLuvHDR(uint packedColor)` | 从 32 位 LogLuv HDR 解码 RGB |

## 依赖关系
### import
- `Utils.Math.MathHelpers`
- `Utils.Math.FormatConversion`
- `Utils.Color.ColorHelpers`

## 实现细节

- LogLuv 编码使用 14 位对数亮度和 2x9 位色度，支持约 10^-6 到 10^6 的亮度范围。
- 八面体法线映射在精度和存储之间提供了良好的平衡。
