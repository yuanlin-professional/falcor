# PBRTCoatedConductorMaterial.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/PBRT/PBRTCoatedConductorMaterial.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials/PBRT

## 功能概述

PBRT 涂层导体材质的实现。在导体表面上覆盖一层电介质界面，使用各向异性 GGX 分别控制界面和导体的粗糙度。导体 eta 和 k 分别编码在 baseColor 和 transColor 中。

## 结构体与接口

### `PBRTCoatedConductorMaterial`

- **继承**: `MaterialBase`, `IMaterial`
- **关联类型**: `PBRTCoatedConductorMaterialInstance`

#### 纹理编码

| 纹理通道 | 含义 |
|----------|------|
| baseColor | 导体 eta（复折射率实部） |
| transColor | 导体 k（消光系数） |
| specular.RG | 界面 X/Y 粗糙度 |
| specular.BA | 导体 X/Y 粗糙度 |

## 依赖关系

### import / export
- `Rendering.Materials.IMaterial`（导出）
- `Rendering.Materials.PBRT.PBRTCoatedConductorMaterialInstance`（导出）
- `Scene.Material.BasicMaterialData`
