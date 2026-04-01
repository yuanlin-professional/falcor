# PBRTCoatedDiffuseMaterial.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/PBRT/PBRTCoatedDiffuseMaterial.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials/PBRT

## 功能概述

PBRT 涂层漫反射材质的实现。在漫反射底层上覆盖一层电介质界面，使用各向异性 GGX 控制界面粗糙度。

## 结构体与接口

### `PBRTCoatedDiffuseMaterial`

- **继承**: `MaterialBase`, `IMaterial`
- **关联类型**: `PBRTCoatedDiffuseMaterialInstance`

## 依赖关系

### import / export
- `Rendering.Materials.IMaterial`（导出）
- `Rendering.Materials.PBRT.PBRTCoatedDiffuseMaterialInstance`（导出）
