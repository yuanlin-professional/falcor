# SDF3DPrimitiveFactory 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SDF3DPrimitiveFactory.h`, `Source/Falcor/Scene/SDFs/SDF3DPrimitiveFactory.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/SDFs

## 功能概述

`SDF3DPrimitiveFactory` 是一个工厂类,提供创建和处理 SDF 3D 基元的静态方法。负责初始化基元通用属性以及计算基元的轴对齐包围盒(AABB)。

## 类与接口

### `SDF3DPrimitiveFactory`

- **继承**: 无
- **职责**: 提供 SDF 3D 基元的工厂方法,包括基元初始化和 AABB 计算

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static SDF3DPrimitive initCommon(SDF3DShapeType, const float3&, float, float, SDFOperationType, const Transform&)` | 初始化基元的通用属性(形状类型、形状数据、膨胀、平滑度、操作类型、变换) |
| `static AABB computeAABB(const SDF3DPrimitive&)` | 根据基元的形状类型、数据和变换计算其轴对齐包围盒 |

## 依赖关系

### 本文件引用
- `SDF3DPrimitiveCommon.slang` (基元数据结构定义)
- `Core/Macros.h`, `Utils/Math/AABB.h`, `Utils/Math/Vector.h`
- `Utils/SDF/SDFOperationType.slang`, `Scene/Transform.h`

### 被以下文件引用
- SDF 编辑器等需要创建基元的模块

## 实现细节

- `initCommon` 从 `Transform` 提取平移向量和逆旋转缩放矩阵(通过 `inverse(float3x3(transform.getMatrix()))`)
- `computeAABB` 根据形状类型计算包围盒:
  - **球体**: 以半径为范围(加上圆角和平滑值)
  - **椭球体/盒子**: 以半尺寸加圆角为范围
  - **环面**: 以大半径和小半径为范围
  - **锥体**: 根据锥角正切值和高度计算
  - **胶囊体**: 以半长度和圆角为范围
- 所有形状计算完后,使用基元的平移和旋转缩放变换对 AABB 进行变换
- 当操作类型为平滑操作(`SmoothUnion` 及以上)时,平滑值也会被加入圆角计算
