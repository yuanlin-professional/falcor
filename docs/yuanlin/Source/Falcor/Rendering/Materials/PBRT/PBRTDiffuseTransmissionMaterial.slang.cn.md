# PBRTDiffuseTransmissionMaterial.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/PBRT/PBRTDiffuseTransmissionMaterial.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials/PBRT

## 功能概述

PBRT 漫反射透射材质的实现。支持独立的反射颜色（baseColor）和透射颜色（transColor）。单面材质，着色法线不翻转。

## 结构体与接口

### `PBRTDiffuseTransmissionMaterial`

- **继承**: `MaterialBase`, `IMaterial`
- **关联类型**: `PBRTDiffuseTransmissionMaterialInstance`

## 依赖关系

### import / export
- `Rendering.Materials.IMaterial`（导出）
- `Rendering.Materials.PBRT.PBRTDiffuseTransmissionMaterialInstance`（导出）
