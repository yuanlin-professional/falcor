# Common 源码文档

> 路径: `Source/Falcor/Utils/Math/Common.h`
> 类型: C++ 头文件
> 模块: Utils/Math

## 功能概述

提供基础的整数数学工具函数模板，包括二的幂判断、向上取整除法和对齐操作。

## 类与接口

无类定义，仅包含自由函数模板。

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `bool isPowerOf2(T a)` | 判断整数是否为 2 的幂 |
| `T div_round_up(T a, T b)` | 向上取整除法 (a / b) |
| `T align_to(T alignment, T value)` | 将整数值对齐到指定对齐边界 |

## 依赖关系
### 本文件引用
- `<type_traits>`

### 被以下文件引用
- 缓冲区分配、纹理管理等需要对齐计算的模块
