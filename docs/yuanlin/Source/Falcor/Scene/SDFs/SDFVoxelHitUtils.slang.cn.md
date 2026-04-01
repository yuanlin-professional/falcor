# SDFVoxelHitUtils.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SDFVoxelHitUtils.slang`
> 类型: Slang 着色器文件
> 模块: Scene/SDFs

## 功能概述

提供 SDF 体素级别的光线-隐式表面交叉测试和梯度计算工具。核心功能是通过球体追踪(Sphere Tracing)算法在体素内部求解光线与三线性插值隐式表面的交点。

## 结构体与接口

### `SDFVoxelHitUtils` (struct)

#### 函数

| 方法签名 | 说明 |
|----------|------|
| `static bool intersectSDFVoxel(float3 rayOrig, float3 rayDir, bool origInside, float4 v0, float4 v1, float tMax, uint maxSteps, out float t)` | 在体素局部空间中求解光线与隐式表面的交点 |
| `static bool intersectSDFVoxelAny(float3 rayOrig, float3 rayDir, bool origInside, float4 v0, float4 v1, float tMax, uint maxSteps)` | 仅判断光线是否与隐式表面相交(不返回 t 值) |
| `static float3 computeNumericGradient(float3 voxelUnitCoords, float offset, float4 v0, float4 v1)` | 使用四面体法数值计算梯度(体素间不连续) |

### `SDFVoxelHitUtils::Solvers` (嵌套 struct)

| 方法签名 | 说明 |
|----------|------|
| `static bool sphereTraceVoxel(...)` | 球体追踪求解交点,返回 t 值 |
| `static bool sphereTraceVoxelAny(...)` | 球体追踪仅判断相交 |

## 条件编译宏

| 宏 | 说明 |
|----|------|
| `SCENE_SDF_VOXEL_INTERSECTION_METHOD` | 选择体素交叉方法 |
| `SCENE_SDF_VOXEL_SPHERE_TRACING` | 球体追踪方法 |

## 依赖关系

### import
- `Scene.SDFs.SDFVoxelCommon`

### include
- `Utils/Math/MathConstants.slangh`

## 实现细节

- 球体追踪算法在体素局部空间 `[0, 1]` 中工作
- 每步使用 `SDFVoxelCommon::sdfVoxelTrilin` 评估当前位置的距离值
- 步长由当前距离值决定,但不小于 `minstep` (0.0001)
- 找到表面后通过线性插值精化交点位置
- 梯度计算使用四面体法:在目标点周围四个对称位置采样,加权求和得到梯度向量
