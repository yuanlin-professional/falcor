# ScalarMath 源码文档

> 路径: `Source/Falcor/Utils/Math/ScalarMath.h`
> 类型: C++ 头文件
> 模块: Utils/Math

## 功能概述

提供完整的标量数学函数库，对标 HLSL/Slang 的内建函数，使 CPU 端代码可以使用相同的数学接口。支持 float、double 和 float16_t 类型。

## 类与接口

无类定义，全部为 `Falcor::math` 命名空间下的模板函数特化。

#### 关键方法分类

##### 布尔归约
| 方法签名 | 说明 |
|----------|------|
| `bool any(T x)` / `bool all(T x)` | 标量布尔检查 |

##### 基本函数
| 方法签名 | 说明 |
|----------|------|
| `T min(T x, T y)` / `T max(T x, T y)` | 最小/最大值 |
| `T clamp(T x, T min_, T max_)` | 裁剪 |
| `T abs(T x)` / `T sign(T x)` | 绝对值/符号 |

##### 浮点检查
| 方法签名 | 说明 |
|----------|------|
| `bool isfinite(T x)` / `bool isinf(T x)` / `bool isnan(T x)` | 浮点状态检查 |

##### 取整
| 方法签名 | 说明 |
|----------|------|
| `T floor/ceil/trunc/round(T x)` | 取整函数族 |

##### 指数/对数
| 方法签名 | 说明 |
|----------|------|
| `T pow/sqrt/rsqrt/exp/exp2/log/log2/log10(T x)` | 指数对数函数族 |

##### 三角函数
| 方法签名 | 说明 |
|----------|------|
| `T radians/degrees(T x)` | 角度/弧度转换 |
| `T sin/cos/tan/asin/acos/atan/atan2(...)` | 三角函数族 |
| `T sinh/cosh/tanh(T x)` | 双曲函数族 |

##### 其他
| 方法签名 | 说明 |
|----------|------|
| `T fmod/frac/lerp/rcp/saturate/step/smoothstep(...)` | HLSL 风格工具函数 |
| `float f16tof32(uint)` / `uint f32tof16(float)` | fp16 转换 |
| `float asfloat(uint32_t/int32_t)` | 位转换为浮点 |
| `uint32_t asuint(float)` / `int32_t asint(float)` | 浮点位转换为整数 |

## 依赖关系
### 本文件引用
- `ScalarTypes.h`
- `<fstd/bit.h>`
- `<cmath>`
