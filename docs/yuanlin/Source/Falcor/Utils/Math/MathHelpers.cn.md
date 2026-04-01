# MathHelpers 源码文档

> 路径: `Source/Falcor/Utils/Math/MathHelpers.h`
> 类型: C++ 头文件
> 模块: Utils/Math

## 功能概述

提供 CPU 端的数学辅助工具函数，包括正交基生成、矩阵有效性检查和变换矩阵验证等功能。

## 类与接口

无类定义，全部为自由函数。

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `float3 perp_stark(const float3& u)` | 生成与输入向量正交的单位向量 |
| `void branchlessONB(const float3 n, float3& b1, float3& b2)` | 无分支正交基生成（Pixar 方法） |
| `void buildFrame(const float3& n, float3& t, float3& b)` | 从法线构建局部坐标系 |
| `bool isMatrixValid(const matrix& m)` | 检查矩阵是否包含 inf/nan |
| `bool isMatrixAffine(const matrix& m)` | 检查矩阵是否为仿射变换 |
| `float4x4 validateTransformMatrix(const float4x4& transform)` | 验证并修正变换矩阵 |

## 依赖关系
### 本文件引用
- `Vector.h`
- `Matrix.h`
- `Core/Error.h`
- `Utils/Logger.h`
