# PBRTCoatedConductorMaterialInstance.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/PBRT/PBRTCoatedConductorMaterialInstance.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials/PBRT

## 功能概述

PBRT 涂层导体材质实例。使用 `LayeredBSDF` 将电介质界面（顶层）和导体（底层）组合。双面均用电介质涂覆导体。

## 结构体与接口

### `PBRTCoatedConductorMaterialInstance`

- **继承**: `MaterialInstanceBase`, `IMaterialInstance`

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `interfaceD` | `AnisotropicGGX` | 电介质界面粗糙度 |
| `conductorD` | `AnisotropicGGX` | 导体粗糙度 |
| `interfaceEta` | `float` | 界面 IOR |
| `conductorEta` / `conductorK` | `float3` | 导体复折射率 |
| `maxDepth` | `int` | 分层随机游走最大深度 |

## 依赖关系

### import / export
- `Rendering.Materials.IMaterialInstance`（导出）
- `Rendering.Materials.AnisotropicGGX`（导出）
- `Rendering.Materials.LayeredBSDF`
- `Rendering.Materials.PBRT.PBRTConductorMaterialInstance`
- `Rendering.Materials.PBRT.PBRTDielectricMaterialInstance`
