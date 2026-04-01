# Ray 源码文档

> 路径: `Source/Falcor/Utils/Math/Ray.h`
> 类型: C++ 头文件
> 模块: Utils/Math

## 功能概述

定义 CPU 端的射线类型 `Ray`，内存布局与 DXR 的 `RayDesc` 完全一致，确保跨 CPU/GPU 的数据兼容性。

## 类与接口
### `Ray`
- **继承**: 无
- **职责**: 表示带参数范围的射线

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `Ray()` | 默认构造 |
| `Ray(float3 origin, float3 dir, float tMin = 0, float tMax = FLT_MAX)` | 指定起点、方向和参数范围 |

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `origin` | `float3` | 射线起点 |
| `tMin` | `float` | 最小参数值 |
| `dir` | `float3` | 射线方向 |
| `tMax` | `float` | 最大参数值 |

## 依赖关系
### 本文件引用
- `Vector.h`

## 实现细节

- 通过 `static_assert` 确保结构体布局与 DXR `RayDesc` 完全匹配（32 字节，特定偏移量）。
