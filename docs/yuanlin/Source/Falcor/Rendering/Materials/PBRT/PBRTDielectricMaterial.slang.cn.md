# PBRTDielectricMaterial.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/PBRT/PBRTDielectricMaterial.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials/PBRT

## 功能概述

PBRT 电介质材质的实现。使用各向异性 GGX 微表面模型处理反射和折射。注意背面命中不翻转着色法线，因为 BSDF 自身处理全球面方向。

## 结构体与接口

### `PBRTDielectricMaterial`

- **继承**: `MaterialBase`, `IMaterial`
- **关联类型**: `PBRTDielectricMaterialInstance`

## 依赖关系

### import / export
- `Rendering.Materials.IMaterial`（导出）
- `Rendering.Materials.PBRT.PBRTDielectricMaterialInstance`（导出）
