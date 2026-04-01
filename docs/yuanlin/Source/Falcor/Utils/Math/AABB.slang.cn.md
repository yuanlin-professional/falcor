# AABB.slang 着色器文档

> 路径: `Source/Falcor/Utils/Math/AABB.slang`
> 类型: Slang 着色器
> 模块: Utils/Math

## 功能概述

提供轴对齐包围盒 (AABB) 的 GPU 端实现，与 CPU 端 `AABB.h` 功能对应。用于着色器中的空间查询、碰撞检测等。

## 结构体与接口

### `AABB`
| 字段 | 类型 | 说明 |
|------|------|------|
| `minPoint` | `float3` | 最小点 |
| `maxPoint` | `float3` | 最大点 |

## 函数
| 函数签名 | 说明 |
|----------|------|
| `__init(float3 minPoint, float3 maxPoint)` | 从最小/最大点初始化 |
| `void set(float3 p)` | 设为单个点 |
| `void set(float3 _min, float3 _max)` | 设置最小/最大点 |
| `void invalidate()` | 使包围盒无效 |
| `bool valid()` | 判断是否有效 |
| `void include(float3 p)` | 扩展以包含点 |
| `void include(AABB b)` | 扩展以包含另一个包围盒 |
| `bool contains(float3 p)` | 判断是否包含某点 |
| `float3 center()` | 返回中心 |
| `float3 extent()` | 返回尺寸 |
| `float area()` | 返回表面积 |
| `float volume()` | 返回体积 |
| `float radius()` | 返回最小外接球半径 |
| `bool intersects(const AABB other)` | 判断两个包围盒是否相交 |
| `float minDistance(float3 p)` | 计算到点的最小距离 |
| `float minDistance(const AABB other)` | 计算到另一个包围盒的最小距离 |

## 依赖关系
### import
- `Utils/Math/MathConstants.slangh`（通过 `#include`）
