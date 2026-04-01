# MatrixMath 源码文档

> 路径: `Source/Falcor/Utils/Math/MatrixMath.h`
> 类型: C++ 头文件
> 模块: Utils/Math

## 功能概述

提供完整的矩阵数学运算库，包括乘法、转置、行列式、求逆、分解、投影矩阵构建、变换矩阵构建等。代码大部分源自 GLM 库。

## 类与接口

无类定义，全部为 `Falcor::math` 命名空间下的模板函数。

#### 关键方法

##### 乘法
| 方法签名 | 说明 |
|----------|------|
| `matrix<T,M,P> mul(const matrix<T,M,N>&, const matrix<T,N,P>&)` | 矩阵-矩阵乘法 |
| `vector<T,R> mul(const matrix<T,R,C>&, const vector<T,C>&)` | 矩阵-列向量乘法 |
| `vector<T,C> mul(const vector<T,R>&, const matrix<T,R,C>&)` | 行向量-矩阵乘法 |
| `vector<T,3> transformPoint(const matrix<T,4,4>&, const vector<T,3>&)` | 变换点（齐次 w=1） |
| `vector<T,3> transformVector(...)` | 变换向量（齐次 w=0） |

##### 函数
| 方法签名 | 说明 |
|----------|------|
| `matrix<T,C,R> transpose(...)` | 矩阵转置 |
| `T determinant(...)` | 行列式（2x2/3x3/4x4） |
| `matrix<T,N,N> inverse(...)` | 矩阵求逆（2x2/3x3/4x4） |
| `bool decompose(...)` | 分解为平移+旋转+缩放+错切+透视 |

##### 构建函数
| 方法签名 | 说明 |
|----------|------|
| `matrix<T,4,4> perspective(...)` | 右手透视投影矩阵（深度映射至 [0,1]） |
| `matrix<T,4,4> ortho(...)` | 右手正交投影矩阵 |
| `matrix<T,4,4> matrixFromTranslation(...)` | 平移矩阵 |
| `matrix<T,4,4> matrixFromRotation(T angle, const vector<T,3>& axis)` | 旋转矩阵 |
| `matrix<T,4,4> matrixFromRotationX/Y/Z(T angle)` | 绕轴旋转矩阵 |
| `matrix<T,4,4> matrixFromRotationXYZ(...)` | XYZ 欧拉角旋转矩阵 |
| `matrix<T,4,4> matrixFromScaling(...)` | 缩放矩阵 |
| `matrix<T,4,4> matrixFromLookAt(...)` | 观察矩阵（支持左右手坐标系） |
| `matrix<T,3,3> matrixFromQuat(const quat<T>&)` | 四元数转旋转矩阵 |
| `matrix<T,R,C> matrixFromColumns(...)` | 从列向量构建矩阵 |
| `matrix<T,N,N> matrixFromDiagonal(...)` | 从对角向量构建对角矩阵 |

## 依赖关系
### 本文件引用
- `MatrixTypes.h`
- `Vector.h`
- `Quaternion.h`
- `Core/Error.h`
- `<fmt/core.h>`
