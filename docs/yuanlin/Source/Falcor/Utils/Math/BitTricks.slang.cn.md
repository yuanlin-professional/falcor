# BitTricks.slang 着色器文档

> 路径: `Source/Falcor/Utils/Math/BitTricks.slang`
> 类型: Slang 着色器
> 模块: Utils/Math

## 功能概述

提供 Morton 码（Z-order 曲线）相关的位交错与反交错工具函数。Morton 码广泛用于空间哈希、纹理压缩、空间填充曲线等场景，可将多维坐标编码为一维索引。

## 函数
| 函数签名 | 说明 |
|----------|------|
| `uint interleave_32bit(uint2 v)` | 将两个 16 位值交错为 32 位 Morton 码 |
| `uint interleave_16bit(uint2 v)` | 将两个 8 位值交错为 16 位 Morton 码 |
| `uint2 deinterleave_16bit(uint i)` | 16 位 Morton 码反交错，返回两个 8 位值 |
| `uint2 deinterleave_8bit(uint i)` | 8 位 Morton 码反交错，返回两个 4 位值 |
| `uint2 deinterleave_2x8bit(uint i)` | 同时反交错两组 8 位 Morton 码 |

## 依赖关系
### import
- 无外部依赖

## 实现细节

- 使用经典的位操作技巧（bit twiddling）实现交错与反交错，参考 Fabian Giesen 的博客文章。
- 函数命名以输出/输入位宽命名，例如 `interleave_16bit` 输出 16 位结果，`deinterleave_16bit` 输入 16 位值。
