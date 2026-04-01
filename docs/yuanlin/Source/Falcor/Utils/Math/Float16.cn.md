# Float16 源码文档

> 路径: `Source/Falcor/Utils/Math/Float16.h` + `Source/Falcor/Utils/Math/Float16.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/Math

## 功能概述

实现 IEEE 754 半精度浮点数 (float16/fp16) 类型。提供 float32 与 float16 之间的转换，以及完整的算术运算符和特殊值检测。

## 类与接口
### `float16_t`
- **继承**: 无
- **职责**: 16 位半精度浮点数的 CPU 端表示

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `float16_t(float value)` | 从 float 构造 |
| `operator float() const` | 隐式转换为 float |
| `static float16_t fromBits(uint16_t bits)` | 从原始位构造 |
| `uint16_t toBits() const` | 获取原始位表示 |
| `bool isFinite() const` | 是否为有限值 |
| `bool isInf() const` | 是否为无穷大 |
| `bool isNan() const` | 是否为 NaN |
| `bool isNormalized() const` | 是否为规格化数 |
| `bool isDenormalized() const` | 是否为非规格化数 |

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `mBits` | `uint16_t` | 原始 16 位存储 |

### 辅助函数
| 函数签名 | 说明 |
|----------|------|
| `uint16_t float32ToFloat16(float value)` | float32 转 float16 |
| `float float16ToFloat32(uint16_t value)` | float16 转 float32 |
| `float16_t operator""h(long double)` | `h` 字面量后缀 |

## 依赖关系
### 本文件引用
- `Core/Macros.h`

### 被以下文件引用
- `ScalarTypes.h`、`ScalarMath.h`、`ScalarJson.h` 等

## 实现细节

- 转换代码源自 GLM 库，处理了规格化/非规格化数、无穷大、NaN 等各种边界情况。
- 算术运算暂时通过提升至 fp32 进行计算，后续可能实现原生 fp16 运算。
- 提供了 `std::numeric_limits<float16_t>` 特化，包含完整的数值极限信息。
