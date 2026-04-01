# FalcorMath 源码文档

> 路径: `Source/Falcor/Utils/Math/FalcorMath.h`
> 类型: C++ 头文件
> 模块: Utils/Math

## 功能概述

提供 Falcor 渲染框架中常用的数学工具函数集合，涵盖相机参数转换、采样函数、坐标变换等。

## 类与接口

无类定义，全部为自由内联函数。

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `float3 mousePosToWorldRay(...)` | 将屏幕空间鼠标位置转换为世界空间射线方向 |
| `float3x3 createMatrixFromBasis(...)` | 从前向/上向量创建旋转矩阵 |
| `float3x3 createMatrixFromLookAt(...)` | 从 look-at 参数创建旋转矩阵 |
| `float3 project2DCrdToUnitSphere(float2 xy)` | 将 2D 坐标投影到单位球面 |
| `float focalLengthToFovY(...)` | 焦距转换为垂直视场角 |
| `float fovYToFocalLength(...)` | 垂直视场角转换为焦距 |
| `float apertureFNumberToRadius(...)` | 光圈 F 值转换为孔径半径 |
| `float apertureRadiusToFNumber(...)` | 孔径半径转换为 F 值 |
| `float radicalInverse(uint32_t i)` | 基数 2 Van der Corput 逆序列 |
| `float3 hammersleyUniform(...)` | Hammersley 均匀半球采样 |
| `float3 hammersleyCosine(...)` | Hammersley 余弦加权半球采样 |
| `float smoothstep(float t)` | 平滑阶梯函数 |

## 依赖关系
### 本文件引用
- `Vector.h`
- `Matrix.h`
- `Quaternion.h`
- `Core/Error.h`
