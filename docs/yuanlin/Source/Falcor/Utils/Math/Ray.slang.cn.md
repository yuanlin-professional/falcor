# Ray.slang 着色器文档

> 路径: `Source/Falcor/Utils/Math/Ray.slang`
> 类型: Slang 着色器
> 模块: Utils/Math

## 功能概述

GPU 端的射线类型，与 DXR `RayDesc` 布局一致，提供额外的初始化和求值功能。

## 结构体与接口

### `Ray`
| 字段 | 类型 | 说明 |
|------|------|------|
| `origin` | `float3` | 射线起点 |
| `tMin` | `float` | 最小参数值 |
| `dir` | `float3` | 射线方向 |
| `tMax` | `float` | 最大参数值 |

## 函数
| 函数签名 | 说明 |
|----------|------|
| `__init(float3 origin, float3 dir, float tMin = 0, float tMax = FLT_MAX)` | 初始化射线 |
| `RayDesc toRayDesc()` | 转换为 DXR RayDesc |
| `float3 eval(float t)` | 计算射线上参数 t 处的位置 |

## 依赖关系
### import
- `Utils/Math/MathConstants.slangh`（通过 `#include`）
