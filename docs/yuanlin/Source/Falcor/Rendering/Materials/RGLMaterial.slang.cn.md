# RGLMaterial.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/RGLMaterial.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

实现基于 RGL 实测数据的材质。在模式生成阶段预计算坐标扭曲、Sigma 值和反照率查找。

## 结构体与接口

### `RGLMaterial`

- **继承**: `MaterialBase`, `IMaterial`
- **关联类型**: `RGLMaterialInstance`

## 依赖关系

### import / export
- `Rendering.Materials.IMaterial`（导出）
- `Rendering.Materials.RGLMaterialInstance`（导出）
- `Rendering.Materials.RGLCommon`
- `Scene.Material.RGLMaterialData`
