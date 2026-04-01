# Rectangle 源码文档

> 路径: `Source/Falcor/Utils/Math/Rectangle.h` + `Source/Falcor/Utils/Math/Rectangle.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/Math

## 功能概述

提供二维轴对齐矩形（UV 图块）的实现，类似于 `AABB` 的二维版本。用于 UV 空间的包围区域计算。

## 类与接口
### `Rectangle`
- **继承**: 无
- **职责**: 表示二维轴对齐矩形，支持合并、相交、包含检测等操作

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `Rectangle()` | 默认构造（无效矩形） |
| `Rectangle(const float2& p)` | 从单个点构造 |
| `Rectangle(const float2& pmin, const float2& pmax)` | 从最小/最大点构造 |
| `void invalidate()` | 使矩形无效 |
| `bool valid() const` | 判断是否有效 |
| `Rectangle& include(const float2& p)` | 扩展以包含点 |
| `Rectangle& include(const Rectangle& b)` | 扩展以包含另一个矩形 |
| `Rectangle& intersection(const Rectangle& b)` | 计算交集 |
| `bool overlaps(Rectangle b)` | 判断是否重叠 |
| `bool contains(const Rectangle& b)` | 判断是否完全包含 |
| `float2 center() const` | 返回中心 |
| `float2 extent() const` | 返回尺寸 |
| `float area() const` | 返回面积 |
| `float radius() const` | 返回最小外接圆半径 |

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `minPoint` | `float2` | 最小点 |
| `maxPoint` | `float2` | 最大点 |

## 依赖关系
### 本文件引用
- `Core/Macros.h`
- `Utils/Math/Vector.h`
- `Utils/Scripting/ScriptBindings.h`（.cpp）

## 实现细节

- `.cpp` 文件通过 `FALCOR_SCRIPT_BINDING` 将 `Rectangle` 暴露给 Python 脚本系统。
