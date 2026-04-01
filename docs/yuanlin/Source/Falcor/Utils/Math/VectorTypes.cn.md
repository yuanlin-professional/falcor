# VectorTypes 源码文档

> 路径: `Source/Falcor/Utils/Math/VectorTypes.h`
> 类型: C++ 头文件
> 模块: Utils/Math

## 功能概述

定义 Falcor 数学系统的向量类型模板 `vector<T, N>`。支持 1-4 维度，语义与 Slang 对齐。提供 HLSL 风格的分量访问（xyzw/rgba/stpq）和 swizzle 操作。

## 类与接口
### `vector<T, N>` (N = 1, 2, 3, 4)
- **继承**: 无
- **职责**: 通用 N 维向量类型

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `vector()` | 默认构造 |
| `vector(T x, ...)` | 分量构造 |
| `vector(T scalar)` | 标量广播构造 |
| `value_type& operator[](size_t i)` | 分量索引访问 |

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `x`/`r`/`s` | `T` | 第一分量（union 多名称访问） |
| `y`/`g`/`t` | `T` | 第二分量（2D+） |
| `z`/`b`/`p` | `T` | 第三分量（3D+） |
| `w`/`a`/`q` | `T` | 第四分量（4D） |

### 类型别名（部分）
| 别名 | 说明 |
|------|------|
| `bool1/2/3/4` | 布尔向量 |
| `int1/2/3/4` | 整数向量 |
| `uint1/2/3/4` | 无符号整数向量 |
| `float1/2/3/4` | 单精度浮点向量 |
| `float16_t1/2/3/4` | 半精度浮点向量 |

## 依赖关系
### 本文件引用
- `ScalarTypes.h`

### 被以下文件引用
- `VectorMath.h`、`Vector.h` 及整个数学系统

## 实现细节

- 使用 union 实现多名称分量访问（x/r/s 指向同一数据）。
- Swizzle 操作通过包含的 `.inl.h` 文件实现（`VectorSwizzle2.inl.h`、`VectorSwizzle3.inl.h`、`VectorSwizzle4.inl.h`）。
- 支持不同维度向量间的隐式截断和扩展构造。
