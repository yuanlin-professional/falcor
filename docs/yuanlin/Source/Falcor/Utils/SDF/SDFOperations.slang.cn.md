# SDFOperations.slang 着色器文档

> 路径: `Source/Falcor/Utils/SDF/SDFOperations.slang`
> 类型: Slang 着色器文件
> 模块: Utils/SDF

## 功能概述

定义了有符号距离场（SDF）的布尔运算和平滑布尔运算函数。包括并集（Union）、交集（Intersection）、差集（Subtraction）三种基本 CSG 操作，以及对应的平滑版本。每种操作同时提供标准标量版本和区间算术版本，后者用于加速空间查询。平滑操作基于 Inigo Quilez 的 smin/smax 算法。混合操作（Blend）基于 RTR4 方程 17.57。

## 函数

### 混合操作（Blend）

| 函数签名 | 说明 |
|----------|------|
| `float sdfBlend(float distance1, float distance2, float radius)` | 基于混合半径对两个 SDF 对象进行平滑混合。`radius` 控制混合区域宽度。参考 RTR4 方程 17.57 |
| `float2 sdfIntervalBlend(float2 distance1, float2 distance2, float radius)` | `sdfBlend` 的区间算术版本 |

### 辅助函数

| 函数签名 | 说明 |
|----------|------|
| `float smin(float a, float b, float k)` | 平滑最小值函数，用于平滑并集运算。参考 Inigo Quilez 的 smin 算法 |
| `float2 intervalSMin(float2 a, float2 b, float k)` | `smin` 的区间算术版本 |
| `float smax(float a, float b, float k)` | 平滑最大值函数，用于平滑交集运算 |
| `float2 intervalSMax(float2 a, float2 b, float k)` | `smax` 的区间算术版本 |

### 基本布尔操作

| 函数签名 | 说明 |
|----------|------|
| `float sdfUnion(float a, float b)` | 并集：取两个距离的最小值 `min(a, b)` |
| `float sdfIntersection(float a, float b)` | 交集：取两个距离的最大值 `max(a, b)` |
| `float sdfSubtraction(float a, float b)` | 差集：`max(a, -b)`，从 `a` 中减去 `b` |
| `float2 sdfIntervalUnion(float2 a, float2 b)` | 并集的区间算术版本 |
| `float2 sdfIntervalIntersection(float2 a, float2 b)` | 交集的区间算术版本 |
| `float2 sdfIntervalSubtraction(float2 a, float2 b)` | 差集的区间算术版本 |

### 平滑布尔操作

| 函数签名 | 说明 |
|----------|------|
| `float sdfSmoothUnion(float a, float b, float k)` | 平滑并集：使用 `smin` 实现带平滑过渡的并集。`k` 控制平滑半径 |
| `float sdfSmoothIntersection(float a, float b, float k)` | 平滑交集：使用 `smax` 实现带平滑过渡的交集 |
| `float sdfSmoothSubtraction(float a, float b, float k)` | 平滑差集：使用 `smax(a, -b, k)` 实现带平滑过渡的差集 |
| `float2 sdfIntervalSmoothUnion(float2 a, float2 b, float k)` | 平滑并集的区间算术版本 |
| `float2 sdfIntervalSmoothIntersection(float2 a, float2 b, float k)` | 平滑交集的区间算术版本 |
| `float2 sdfIntervalSmoothSubtraction(float2 a, float2 b, float k)` | 平滑差集的区间算术版本 |

## 依赖关系

### import

- `Utils.Math.IntervalArithmetic` — 区间算术运算库（`ivlSaturate`、`ivlAdd`、`ivlSub`、`ivlMul`、`ivlDiv`、`ivlNegate`、`ivlAbs`、`ivlMin`、`ivlMax`、`ivlPosSquare` 等）
