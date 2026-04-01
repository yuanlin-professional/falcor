# PBRTDiffuseMaterial.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/PBRT/PBRTDiffuseMaterial.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials/PBRT

## 功能概述

PBRT 漫反射材质的实现。最简单的 PBRT 材质，仅使用基础颜色。支持法线贴图和可微分渲染。

## 结构体与接口

### `PBRTDiffuseMaterial`

- **继承**: `MaterialBase`, `IMaterial`
- **关联类型**: `PBRTDiffuseMaterialInstance`

## 依赖关系

### import / export
- `Rendering.Materials.IMaterial`（导出）
- `Rendering.Materials.PBRT.PBRTDiffuseMaterialInstance`（导出）
- `Scene.Material.PBRT.PBRTDiffuseMaterialParamLayout`
