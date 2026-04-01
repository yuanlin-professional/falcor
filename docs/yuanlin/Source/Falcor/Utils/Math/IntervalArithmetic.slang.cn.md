# IntervalArithmetic.slang 着色器文档

> 路径: `Source/Falcor/Utils/Math/IntervalArithmetic.slang`
> 类型: Slang 着色器
> 模块: Utils/Math

## 功能概述

提供区间算术（Interval Arithmetic）的完整工具函数库。区间用 `float2` 表示，其中 `.x` 为最小值，`.y` 为最大值。广泛用于保守估计和鲁棒性几何计算。

## 函数

### 基本操作
| 函数签名 | 说明 |
|----------|------|
| `float2 ivlCombine(float2 a, float2 b)` | 合并两个区间 |
| `bool ivlValid(float2 interval)` | 检查区间有效性 |
| `bool ivlContainsZero(float2 interval)` | 检查区间是否包含零 |

### 算术运算
| 函数签名 | 说明 |
|----------|------|
| `float2 ivlAdd(...)` | 区间加法（标量/区间） |
| `float2 ivlSub(...)` | 区间减法（标量/区间） |
| `float2 ivlMul(...)` | 区间乘法（标量/区间），含正/负优化版本 |
| `float2 ivlDiv(...)` | 区间除法（标量/区间） |
| `float2 ivlAbs(float2)` | 区间绝对值 |
| `float2 ivlSquare(float2)` | 区间平方 |
| `float2 ivlSqrt(float2)` | 区间平方根 |
| `float2 ivlNegate(float2)` | 区间取反 |

### 范围限制
| 函数签名 | 说明 |
|----------|------|
| `float2 ivlMin(...)` / `ivlMax(...)` | 区间最小/最大值 |
| `float2 ivlClamp(...)` | 区间裁剪 |
| `float2 ivlSaturate(float2)` | 区间饱和 [0,1] |

### 向量长度
| 函数签名 | 说明 |
|----------|------|
| `float2 ivlLength(float2, float2)` | 2D 区间向量长度 |
| `float2 ivlLength(float2, float2, float2)` | 3D 区间向量长度 |
| `float2 ivlLength(float2, float2, float2, float2)` | 4D 区间向量长度 |

## 依赖关系
### import
- `Utils/Math/MathConstants.slangh`（通过 `#include`）
