# PBRTConductorMaterialInstance.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/PBRT/PBRTConductorMaterialInstance.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials/PBRT

## 功能概述

PBRT 导体材质实例，包含 `PBRTConductorBSDF`（仅反射的导体 BSDF）和材质实例封装。使用导体 Fresnel 方程和各向异性 GGX 微表面模型。

## 结构体与接口

### `PBRTConductorBSDF`

- **实现**: `IBSDF`, `IDifferentiable`
- **职责**: 导体镜面反射

| 成员 | 类型 | 说明 |
|------|------|------|
| `D` | `AnisotropicGGX` | 各向异性粗糙度 |
| `eta` | `float3` | 复折射率实部 |
| `k` | `float3` | 消光系数 |

### `PBRTConductorMaterialInstance`

- **继承**: `MaterialInstanceBase`, `IMaterialInstance`
- **叶瓣类型**: Delta/Specular Reflection
- 支持可微分求值（evalAD）

## 依赖关系

### import / export
- `Rendering.Materials.IMaterialInstance`（导出）
- `Rendering.Materials.AnisotropicGGX`（导出）
- `Rendering.Materials.Fresnel`
- `DiffRendering.SceneGradients`（导出）
- `DiffRendering.GradientIOWrapper`（导出）
