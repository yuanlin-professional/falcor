# BC4Encode.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SparseBrickSet/BC4Encode.slang`
> 类型: Slang 着色器文件
> 模块: Scene/SDFs/SparseBrickSet

## 功能概述

实现 BC4 纹理压缩编码算法,用于在 GPU 上将 4x4 的 8 位 snorm 值块压缩为 64 位的 BC4 格式。SDFSBS 使用此模块对砖块数据进行有损压缩以减少内存占用。

## 结构体与接口

### `Codebook`

| 成员 | 类型 | 说明 |
|------|------|------|
| `codes[8]` | `int[8]` | 8 个插值码本值 |

### `BlockIndices`

| 成员 | 类型 | 说明 |
|------|------|------|
| `indices[16]` | `uint[16]` | 4x4 块中每个像素的码本索引 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void fixRange(inout int min, inout int max, int steps)` | 确保最小/最大值范围不小于步数 |
| `void rowRange(int4 row, ...)` | 计算一行值的 5-alpha 和 7-alpha 范围 |
| `int fitCodes(int4x4 block, Codebook, out BlockIndices)` | 将块中每个值匹配到码本中最近的条目,返回总误差 |
| `uint64_t writeAlphaBlock(int alpha0, int alpha1, BlockIndices)` | 将两个端点和索引编码为 64 位压缩块 |
| `uint64_t writeAlphaBlock5(int alpha0, int alpha1, BlockIndices)` | 5-alpha 模式编码(含端点交换逻辑) |
| `uint64_t writeAlphaBlock7(int alpha0, int alpha1, BlockIndices)` | 7-alpha 模式编码(含端点交换逻辑) |
| `uint2 compressBlock(int4x4 block)` | 主压缩函数:选择 5-alpha 或 7-alpha 中误差更小的方案 |

## 依赖关系

### include
- `Utils/Math/MathConstants.slangh`

### 被以下文件引用
- `SDFSBSCreateBricksFromChunks.cs.slang`, `SDFSBSCreateBricksFromSDField.cs.slang`

## 实现细节

- BC4 格式使用两个端点值和 16 个 3 位索引,总计 64 位
- 5-alpha 模式:5 个插值值 + 两个特殊值(-128 和 127),适合含极值的块
- 7-alpha 模式:7 个插值值,适合连续值范围的块
- 算法同时尝试两种模式,选择总平方误差更小的方案
- 端点交换时需相应调整索引映射
