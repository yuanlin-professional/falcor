# PBRTDiffuseTransmissionMaterialInstance.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/PBRT/PBRTDiffuseTransmissionMaterialInstance.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials/PBRT

## 功能概述

PBRT 漫反射透射材质实例。根据入射和出射方向是否在同一半球，选择反射或透射反照率。采样时按反射/透射反照率的亮度比例选择半球。

## 结构体与接口

### `PBRTDiffuseTransmissionMaterialInstance`

- **继承**: `MaterialInstanceBase`, `IMaterialInstance`

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `sf` | `ShadingFrame` | 世界空间着色帧 |
| `albedoR` | `float3` | 反射反照率 |
| `albedoT` | `float3` | 透射反照率 |

#### 叶瓣类型
- `DiffuseReflection` + `DiffuseTransmission`

#### 采样策略
按亮度比例 PT/(PR+PT) 选择透射或反射半球，然后在所选半球内余弦加权采样。

## 依赖关系

### import / export
- `Rendering.Materials.IMaterialInstance`（导出）
- `Utils.Color.ColorHelpers`
