# Raytracing 源码文档

> 路径: `Source/Falcor/Core/API/Raytracing.h`
> 类型: C++ 头文件
> 模块: Core/API

## 功能概述

Raytracing.h 定义了光线追踪管线相关的枚举和数据结构，包括管线标志、射线标志和轴对齐包围盒（AABB）。

## 类与接口

### `RtPipelineFlags`（枚举）
| 值 | 说明 |
|----|------|
| `None` | 无标志 |
| `SkipTriangles` | 跳过三角形 |
| `SkipProceduralPrimitives` | 跳过程序化图元 |

### `RtAABB`（结构体）
| 成员 | 类型 | 说明 |
|------|------|------|
| `min` | `float3` | 最小角 |
| `max` | `float3` | 最大角 |

### `RayFlags`（枚举）
光线追踪射线标志，与设备端定义一致：ForceOpaque、ForceNonOpaque、AcceptFirstHitAndEndSearch、SkipClosestHitShader、CullBackFacingTriangles 等。

### 常量函数
| 函数 | 说明 |
|------|------|
| `getRaytracingMaxAttributeSize()` | 返回最大光线追踪属性大小（32 字节） |

## 依赖关系
### 本文件引用
- `Core/Macros.h`, `Utils/Math/Vector.h`

### 被以下文件引用
- `RtStateObject.h`, `RenderContext.h`
