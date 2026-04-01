# PBRTConductorMaterial.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/PBRT/PBRTConductorMaterial.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials/PBRT

## 功能概述

PBRT 导体材质的实现。使用复折射率（eta + ik）描述导体反射特性，支持各向异性 GGX 粗糙度和可微分渲染。

## 结构体与接口

### `PBRTConductorMaterial`

- **继承**: `MaterialBase`, `IMaterial`
- **关联类型**: `PBRTConductorMaterialInstance`

#### 纹理编码

| 纹理 | 含义 |
|------|------|
| baseColor | 导体 eta |
| transColor | 导体 k |
| specular.RG | X/Y 粗糙度 |

## 依赖关系

### import / export
- `Rendering.Materials.IMaterial`（导出）
- `Rendering.Materials.PBRT.PBRTConductorMaterialInstance`（导出）
- `Scene.Material.PBRT.PBRTConductorMaterialParamLayout`
