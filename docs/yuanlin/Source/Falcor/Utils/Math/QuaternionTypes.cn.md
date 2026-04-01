# QuaternionTypes 源码文档

> 路径: `Source/Falcor/Utils/Math/QuaternionTypes.h`
> 类型: C++ 头文件
> 模块: Utils/Math

## 功能概述

定义四元数类型模板 `quat<T>`。四元数以 (x, y, z, w) 存储，便于与 GPU 互操作。

## 类与接口
### `quat<T>`
- **继承**: 无
- **职责**: 表示四元数 q = w + xi + yj + zk

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `quat()` | 默认构造为单位四元数 (0,0,0,1) |
| `quat(const vector<T,3>& xyz, const T& w)` | 从虚部向量和实部构造 |
| `quat(x, y, z, w)` | 从四个分量构造 |
| `static quat identity()` | 单位四元数 |
| `value_type& operator[](size_t i)` | 分量访问 |

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `x, y, z` | `T` | 虚部分量 |
| `w` | `T` | 实部分量 |

### 类型别名
| 别名 | 说明 |
|------|------|
| `quatf` | float 四元数 |

## 依赖关系
### 本文件引用
- `ScalarTypes.h`
- `VectorTypes.h`
