# PBRTDielectricMaterialInstance.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/PBRT/PBRTDielectricMaterialInstance.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials/PBRT

## 功能概述

PBRT 电介质材质实例，包含 `PBRTDielectricBSDF`（全球面电介质 BSDF）和材质实例封装。支持反射和折射，使用各向异性 GGX 和电介质 Fresnel 方程。BSDFContext 根据正面/背面命中设置 IOR。

## 结构体与接口

### `PBRTDielectricBSDF`

- **实现**: `IBSDF`

| 成员 | 类型 | 说明 |
|------|------|------|
| `D` | `AnisotropicGGX` | 各向异性粗糙度 |
| `eta` | `float` | 绝对折射率 |

#### 叶瓣类型
- 光滑: Delta Reflection + Delta Transmission
- 粗糙: Specular Reflection + Specular Transmission

### `PBRTDielectricMaterialInstance`

- **继承**: `MaterialInstanceBase`, `IMaterialInstance`
- 单面材质，着色法线不翻转

## 依赖关系

### import / export
- `Rendering.Materials.IMaterialInstance`（导出）
- `Rendering.Materials.AnisotropicGGX`（导出）
- `Rendering.Materials.Fresnel`
