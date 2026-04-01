# AABB 源码文档

> 路径: `Source/Falcor/Utils/Math/AABB.h` + `Source/Falcor/Utils/Math/AABB.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/Math

## 功能概述

提供轴对齐包围盒 (Axis-Aligned Bounding Box, AABB) 的 CPU 端实现。AABB 以最小点和最大点存储，支持合并、相交、变换等常用几何操作。GPU 端有等价的 Slang 实现（见 `AABB.slang`）。

## 类与接口
### `AABB`
- **继承**: 无
- **职责**: 表示三维轴对齐包围盒，提供有效性判断、包含检测、变换、面积/体积计算等功能

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `AABB()` | 默认构造，初始化为 +/-inf（无效盒） |
| `AABB(const float3& p)` | 从单个点构造 |
| `AABB(const float3& pmin, const float3& pmax)` | 从最小/最大点构造 |
| `void invalidate()` | 将包围盒重置为无效状态 |
| `bool valid() const` | 判断包围盒是否有效 |
| `AABB& include(const float3& p)` | 扩展包围盒以包含指定点 |
| `AABB& include(const AABB& b)` | 扩展包围盒以包含另一个包围盒 |
| `AABB& intersection(const AABB& b)` | 计算与另一个包围盒的交集 |
| `bool overlaps(AABB b)` | 判断两个包围盒是否重叠 |
| `bool contains(const AABB& b)` | 判断是否完全包含另一个包围盒 |
| `float3 center() const` | 返回包围盒中心 |
| `float3 extent() const` | 返回包围盒尺寸 |
| `float area() const` | 返回表面积 |
| `float volume() const` | 返回体积 |
| `float radius() const` | 返回最小外接球半径 |
| `AABB transform(const float4x4& mat) const` | 矩阵变换后的包围盒 |

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `minPoint` | `float3` | 最小点 |
| `maxPoint` | `float3` | 最大点，若任一分量 minPoint > maxPoint 则包围盒无效 |

## 依赖关系
### 本文件引用
- `Core/Macros.h`
- `Core/API/Raytracing.h`（`RtAABB` 类型）
- `Utils/Math/Matrix.h`
- `Utils/Math/Vector.h`
- `Utils/Scripting/ScriptBindings.h`（.cpp 中用于 Python 绑定）

### 被以下文件引用
- 场景管理、BVH 构建、光线追踪等模块广泛使用

## 实现细节

- `.cpp` 文件通过 `FALCOR_SCRIPT_BINDING` 宏将 `AABB` 类暴露给 Python 脚本系统，支持通过 pybind11 进行构造、属性访问和运算符操作。
- 支持与 `RtAABB`（DXR 光线追踪 AABB 类型）之间的显式转换。
- `transform` 方法使用列向量展开法高效计算变换后包围盒，避免枚举所有 8 个顶点。
