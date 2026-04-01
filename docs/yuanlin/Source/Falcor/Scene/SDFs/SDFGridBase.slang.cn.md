# SDFGridBase.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SDFGridBase.slang`
> 类型: Slang 着色器文件
> 模块: Scene/SDFs

## 功能概述

定义了 SDF 网格的基础接口 `ISDFGrid` 和基类 `SDFGridBase`。`ISDFGrid` 接口规定了所有 SDF 网格实现必须提供的光线-SDF 交叉测试和命中解码方法。`SDFGridBase` 提供所有实现共享的常量和辅助函数。

## 结构体与接口

### `ISDFGrid` (interface)

- **职责**: 定义 SDF 网格的统一交叉测试接口

| 方法签名 | 说明 |
|----------|------|
| `bool intersectSDF(float3 rayOrigin, float3 rayDir, float tMin, float tMax, uint primitiveID, out float t, out SDFGridHitData hitData)` | 对 SDF 网格进行光线交叉测试,返回交叉信息 |
| `bool intersectSDFAny(float3 rayOrigin, float3 rayDir, float tMin, float tMax, uint primitiveID)` | 仅判断光线是否与 SDF 网格相交,不返回详细信息 |
| `void decodeHit(SDFGridHitData hitData, float3 hitPosition, out float3 gradient)` | 解码命中点并计算该位置的梯度 |

### `SDFGridBase` (open struct)

- **职责**: 提供 SDF 网格的共享基础功能

| 成员/方法 | 说明 |
|-----------|------|
| `kSolverMaxStepCount` | 求解器最大步数(由 `SCENE_SDF_SOLVER_MAX_ITERATION_COUNT` 宏定义) |
| `kSnormErrorTolerance` | 8 位 snorm 格式引入的误差容限 (0.6/256) |
| `calculateGridWidthFromLevel(uint level)` | 根据层级计算网格宽度 (`1 << level`) |

## 依赖关系

### import
- `Scene.SDFs.SDFVoxelCommon`
- `Utils.Math.FormatConversion`
- `Scene.SDFs.SDFGridHitData` (导出)
