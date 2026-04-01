# CubicSpline 源码文档

> 路径: `Source/Falcor/Utils/Math/CubicSpline.h`
> 类型: C++ 头文件
> 模块: Utils/Math

## 功能概述

实现三次样条插值（Natural Cubic Spline）。支持基于位置的样条和基于位置+时间的样条两种模式，常用于相机动画路径、关键帧插值等。

## 类与接口
### `CubicSpline<T>`
- **继承**: 无
- **职责**: 根据控制点构建三次样条曲线，并提供插值查询功能

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `CubicSpline(const T* controlPoints, uint32_t pointCount)` | 从控制点数组构造位置样条 |
| `CubicSpline(const T* points, uint32_t pointCount, float const* durations)` | 从控制点和时间间隔构造时间样条 |
| `CubicSpline& setup(const T* controlPoints, uint32_t pointCount)` | 设置位置样条 |
| `CubicSpline& setup(const T* points, uint32_t pointCount, float const* durations)` | 设置时间样条 |
| `T interpolate(uint32_t section, float point) const` | 在指定段进行插值 |
| `void clear()` | 清除样条数据 |

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `mCoefficient` | `fast_vector<CubicCoeff>` | 每段的三次多项式系数 (a, b, c, d) |

## 依赖关系
### 本文件引用
- `Utils/fast_vector.h`
- `<vector>`, `<cstdint>`

### 被以下文件引用
- 相机动画系统

## 实现细节

- 使用三对角矩阵算法（Thomas 算法）求解自然三次样条系数。
- 时间版本的样条支持非均匀时间间隔，基于 Hermite 样条转换公式。
- 插值使用 Horner 法则进行高效多项式求值。
