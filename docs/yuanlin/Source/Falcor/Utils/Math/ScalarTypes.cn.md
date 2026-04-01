# ScalarTypes 源码文档

> 路径: `Source/Falcor/Utils/Math/ScalarTypes.h`
> 类型: C++ 头文件
> 模块: Utils/Math

## 功能概述

定义 Falcor 数学系统的标量类型基础设施，包括基本类型别名、类型特征 (type traits)、手偏性枚举以及格式化支持。

## 类与接口

### `Handedness` 枚举
| 值 | 说明 |
|----|------|
| `RightHanded` | 右手坐标系 |
| `LeftHanded` | 左手坐标系 |

### `ScalarTraits<T>` 特征模板
为每种标量类型提供名称字符串：bool、int、uint、float、double、float16_t。

### 类型特征
| 特征 | 说明 |
|------|------|
| `is_bool_v<T>` | 是否为 bool |
| `is_int_v<T>` | 是否为 int32_t |
| `is_uint_v<T>` | 是否为 uint32_t |
| `is_float_v<T>` | 是否为 float |
| `is_double_v<T>` | 是否为 double |
| `is_float16_t_v<T>` | 是否为 float16_t |
| `is_arithmetic_v<T>` | 是否为算术类型（含 float16_t） |
| `is_floating_point_v<T>` | 是否为浮点类型（含 float16_t） |

### 类型别名
| 别名 | 说明 |
|------|------|
| `uint` | `uint32_t` 别名 |

## 依赖关系
### 本文件引用
- `Float16.h`
- `<fmt/core.h>`
