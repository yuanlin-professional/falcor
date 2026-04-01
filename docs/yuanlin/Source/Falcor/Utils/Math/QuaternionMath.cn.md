# QuaternionMath 源码文档

> 路径: `Source/Falcor/Utils/Math/QuaternionMath.h`
> 类型: C++ 头文件
> 模块: Utils/Math

## 功能概述

CPU 端完整的四元数数学运算库，源自 GLM 库。包括算术运算、乘法、几何运算、插值、欧拉角转换和各种构建函数。

## 类与接口

无类定义，全部为 `Falcor::math` 命名空间下的模板函数。

#### 关键方法

##### 乘法与变换
| 方法签名 | 说明 |
|----------|------|
| `quat<T> mul(const quat<T>&, const quat<T>&)` | 四元数乘法 |
| `vector<T,3> mul(const quat<T>&, const vector<T,3>&)` | 四元数旋转向量 |
| `vector<T,3> transformVector(const quat<T>&, const vector<T,3>&)` | 向量变换 |

##### 几何运算
| 方法签名 | 说明 |
|----------|------|
| `T dot(const quat<T>&, const quat<T>&)` | 点积 |
| `T length(const quat<T>&)` | 模长 |
| `quat<T> normalize(const quat<T>&)` | 归一化 |
| `quat<T> conjugate(const quat<T>&)` | 共轭 |
| `quat<T> inverse(const quat<T>&)` | 逆 |

##### 插值
| 方法签名 | 说明 |
|----------|------|
| `quat<T> lerp(...)` | 线性插值 |
| `quat<T> slerp(...)` | 球面线性插值 |

##### 欧拉角
| 方法签名 | 说明 |
|----------|------|
| `T pitch(const quat<T>&)` | 提取俯仰角 |
| `T yaw(const quat<T>&)` | 提取偏航角 |
| `T roll(const quat<T>&)` | 提取翻滚角 |
| `vector<T,3> eulerAngles(const quat<T>&)` | 提取欧拉角向量 |

##### 构建函数
| 方法签名 | 说明 |
|----------|------|
| `quat<T> quatFromAngleAxis(T angle, const vector<T,3>& axis)` | 从角度和轴构建 |
| `quat<T> quatFromRotationBetweenVectors(...)` | 从两个方向间旋转构建 |
| `quat<T> quatFromEulerAngles(const vector<T,3>&)` | 从欧拉角构建 |
| `quat<T> quatFromMatrix(const matrix<T,3,3>&)` | 从旋转矩阵构建 |
| `quat<T> quatFromLookAt(...)` | 从 look-at 参数构建 |

## 依赖关系
### 本文件引用
- `QuaternionTypes.h`
- `ScalarMath.h`
- `VectorMath.h`
- `MathConstants.slangh`
- `Core/Error.h`
