# BCTypes.slang 着色器文档

> 路径: `scripts/python/TinyBC/BCTypes.slang`
> 类型: Slang 着色器（类型定义模块）
> 模块: scripts/python/TinyBC

## 功能概述

该着色器文件定义了 Block Compression（BC）纹理压缩所需的核心数据类型。包含两个主要结构体：`BCWeights` 用于存储 4x4 纹素块的插值权重，`BCTile` 用于存储完整的 BC 块数据（插值权重加两个端点颜色）。两个结构体均实现了 `IDifferentiable` 接口以支持自动微分，并重载了算术运算符以便于梯度下降优化。该文件作为类型定义模块被 `TinyBC.cs.slang` 导入使用。

## 结构体与接口

### `BCWeights` (实现 `IDifferentiable`)

存储 4x4 纹素块的 16 个插值权重。

| 字段 | 类型 | 说明 |
|------|------|------|
| `w` | `float[16]` | 16 个插值权重值，每个对应 4x4 块中的一个纹素 |

**构造函数：**

| 签名 | 说明 |
|------|------|
| `__init(float _w)` | 将所有 16 个权重初始化为相同值 `_w` |
| `__init(float _w[16])` | 从长度为 16 的数组逐一初始化权重 |

**成员方法：**

| 函数签名 | 说明 |
|----------|------|
| `void clamp()` | 将所有权重钳制到 [0, 1] 范围（mutating） |
| `void quantize()` | 量化权重：第一个权重量化到 3 位（7 级），其余量化到 4 位（15 级）（mutating） |
| `BCWeights rsqrt()` | 返回所有权重的平方根倒数 |

### `BCTile` (实现 `IDifferentiable`)

存储一个 4x4 纹素块的完整 BC 压缩数据，包括插值权重和两个端点颜色。

| 字段 | 类型 | 说明 |
|------|------|------|
| `weights` | `BCWeights` | 16 个插值权重 |
| `minEndPoints` | `float4` | 最小端点颜色（RGBA） |
| `maxEndPoints` | `float4` | 最大端点颜色（RGBA） |

**构造函数：**

| 签名 | 说明 |
|------|------|
| `__init()` | 默认构造，所有值初始化为 0 |
| `__init(BCTile.Differential tile)` | 从微分类型构造（用于梯度转换） |
| `__init(float _weight, float4 _minEndPoints, float4 _maxEndPoints)` | 以统一权重和指定端点构造 |

**成员方法：**

| 函数签名 | 说明 |
|----------|------|
| `void clamp()` | 将权重和端点均钳制到 [0, 1] 范围（mutating） |
| `void quantize()` | 量化权重和端点：权重按 `BCWeights.quantize()` 量化，端点量化到 7 位精度（mutating） |
| `BCTile rsqrt()` | 返回所有分量的平方根倒数 |
| `float4 decode(int i)` | 根据权重对两个端点进行线性插值解码第 i 个纹素颜色（支持反向微分） |
| `float4 decodeExact(int i)` | 使用 6 位精度整数插值解码第 i 个纹素颜色，匹配 API 规范的精确行为 |

## 运算符重载

### `BCWeights` 运算符

| 运算符 | 说明 |
|--------|------|
| `float + BCWeights` | 标量与权重逐元素加法 |
| `BCWeights + BCWeights` | 权重逐元素加法 |
| `BCWeights - BCWeights` | 权重逐元素减法 |
| `BCWeights * BCWeights` | 权重逐元素乘法 |
| `float * BCWeights` | 标量与权重逐元素乘法 |

### `BCTile` 运算符

| 运算符 | 说明 |
|--------|------|
| `BCTile * BCTile` | 块数据逐分量乘法 |
| `float * BCTile` | 标量与块数据逐分量乘法 |
| `float + BCTile` | 标量与块数据逐分量加法 |
| `BCTile + BCTile` | 块数据逐分量加法 |
| `BCTile - BCTile` | 块数据逐分量减法 |

## 依赖关系

### import

无外部依赖。该文件是独立的类型定义模块。
