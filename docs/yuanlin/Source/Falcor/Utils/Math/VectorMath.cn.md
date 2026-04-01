# VectorMath 源码文档

> 路径: `Source/Falcor/Utils/Math/VectorMath.h`
> 类型: C++ 头文件
> 模块: Utils/Math

## 功能概述

提供完整的向量数学运算库，对标 HLSL/Slang 的内建向量运算。包括布尔归约、元素级运算符、几何函数（dot、cross、length、normalize 等）、混合函数以及类型转换。

## 类与接口

无类定义，全部为 `Falcor::math` 命名空间下的函数和运算符重载。

#### 关键方法分类

##### 布尔归约
| 方法签名 | 说明 |
|----------|------|
| `bool any(boolN v)` | 任一分量为 true |
| `bool all(boolN v)` | 所有分量为 true |
| `bool none(boolN v)` | 无分量为 true |

##### 运算符
- 一元运算符: `+`, `-`, `~`, `!`
- 二元运算符: `+`, `-`, `*`, `/`, `%`, `&`, `|`, `^`, `<<`, `>>`
- 比较运算符: `==`, `!=`, `<`, `<=`, `>`, `>=`
- 复合赋值运算符: `+=`, `-=`, `*=`, `/=` 等

##### 几何函数
| 方法签名 | 说明 |
|----------|------|
| `T dot(vector<T,N>, vector<T,N>)` | 点积 |
| `vector<T,3> cross(vector<T,3>, vector<T,3>)` | 叉积 |
| `T length(vector<T,N>)` | 向量长度 |
| `vector<T,N> normalize(vector<T,N>)` | 向量归一化 |
| `T distance(...)` | 两点距离 |
| `vector<T,N> reflect(...)` | 反射 |
| `vector<T,N> faceforward(...)` | 面向调整 |

##### 元素级函数
| 方法签名 | 说明 |
|----------|------|
| `min/max/clamp/abs/sign/floor/ceil/round(...)` | 标量函数的向量版本 |
| `lerp/saturate/step/smoothstep(...)` | 插值函数的向量版本 |

## 依赖关系
### 本文件引用
- `VectorTypes.h`
- `ScalarMath.h`
- `<fmt/format.h>`
