# PBRTCoatedDiffuseMaterialInstance.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/PBRT/PBRTCoatedDiffuseMaterialInstance.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials/PBRT

## 功能概述

PBRT 涂层漫反射材质实例。使用 `LayeredBSDF` 将电介质界面（顶层）和漫反射（底层）组合。

## 结构体与接口

### `PBRTCoatedDiffuseMaterialInstance`

- **继承**: `MaterialInstanceBase`, `IMaterialInstance`

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `interfaceD` | `AnisotropicGGX` | 电介质界面粗糙度 |
| `interfaceEta` | `float` | 界面 IOR |
| `diffuseAlbedo` | `float3` | 漫反射反照率 |
| `maxDepth` | `int` | 分层随机游走最大深度 |

#### 叶瓣类型
- `DiffuseReflection` + Delta/Specular Reflection

## 依赖关系

### import
- `Rendering.Materials.LayeredBSDF`
- `Rendering.Materials.PBRT.PBRTDielectricMaterialInstance`
- `Rendering.Materials.PBRT.PBRTDiffuseMaterialInstance`
