# PBRTDiffuseMaterialInstance.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/PBRT/PBRTDiffuseMaterialInstance.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials/PBRT

## 功能概述

PBRT 漫反射材质实例，包含 `PBRTDiffuseBSDF`（Lambert 漫反射）和材质实例封装。支持可微分求值。

## 结构体与接口

### `PBRTDiffuseBSDF`

- **实现**: `IBSDF`, `IDifferentiable`
- **职责**: Lambert 漫反射 f = albedo * cos(theta) / pi

| 成员 | 类型 | 说明 |
|------|------|------|
| `albedo` | `float3` | 漫反射反照率 |

### `PBRTDiffuseMaterialInstance`

- **继承**: `MaterialInstanceBase`, `IMaterialInstance`
- **叶瓣类型**: `DiffuseReflection`
- 粗糙度固定为 1.0

## 依赖关系

### import / export
- `Rendering.Materials.IMaterialInstance`（导出）
- `DiffRendering.SceneGradients`（导出）
- `DiffRendering.GradientIOWrapper`（导出）
