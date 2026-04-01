# SDF3DPrimitiveCommon.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SDF3DPrimitiveCommon.slang`
> 类型: Slang 着色器文件 (Host/Device 共享)
> 模块: Scene/SDFs

## 功能概述

定义了 SDF 3D 基元的核心数据结构,包括形状类型枚举 `SDF3DShapeType` 和基元数据结构 `SDF3DPrimitive`。此文件是主机端(C++)和设备端(GPU)共享的定义,确保两端数据布局一致。

## 结构体与接口

### `SDF3DShapeType` (enum class)

| 枚举值 | 说明 |
|--------|------|
| `Sphere` | 球体,由半径定义(1个浮点数) |
| `Ellipsoid` | 椭球体,由三个轴对齐半径定义(3个浮点数) |
| `Box` | 盒子,由三个轴对齐半尺寸定义(3个浮点数) |
| `Torus` | 环面,由半径定义(1个浮点数),需要膨胀值才有厚度 |
| `Cone` | 锥体,由锥角正切值和高度定义(2个浮点数) |
| `Capsule` | 胶囊体,由半长度定义(1个浮点数),需要膨胀值才有厚度 |
| `Count` | 形状类型计数 |

### `SDF3DPrimitive` (struct)

| 成员 | 类型 | 说明 |
|------|------|------|
| `shapeType` | `SDF3DShapeType` | 形状类型 |
| `shapeData` | `float3` | 形状参数数据 |
| `shapeBlobbing` | `float` | 形状膨胀值 |
| `operationType` | `SDFOperationType` | CSG 操作类型 |
| `operationSmoothing` | `float` | 操作平滑值 |
| `translation` | `float3` | 平移向量 |
| `invRotationScale` | `float3x3` | 逆旋转缩放矩阵 |

## 依赖关系

### import / include
- `Utils/HostDeviceShared.slangh`
- `Utils/SDF/SDFOperationType.slang` (主机端通过 `#include`,设备端通过 `import`)

### 被以下文件引用
- `SDF3DPrimitive.slang`, `SDF3DPrimitiveFactory.h`, `SDFGrid.h`
- 所有需要访问 SDF 基元数据的着色器文件
