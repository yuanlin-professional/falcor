# MERLMixMaterial.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/MERLMixMaterial.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

实现 MERLMix 材质，支持通过索引贴图在多个 MERL BRDF 之间空间变化。使用 8 位 unorm 红色通道作为 BRDF 索引。

## 结构体与接口

### `MERLMixMaterial`

- **继承**: `MaterialBase`, `IMaterial`
- **关联类型**: `MERLMixMaterialInstance`

#### 关键功能
- 支持法线贴图
- 通过索引贴图选择不同的 MERL BRDF
- 从预计算 LUT 获取反照率

## 依赖关系

### import / export
- `Rendering.Materials.IMaterial`（导出）
- `Rendering.Materials.MERLMixMaterialInstance`（导出）
- `Scene.Material.MERLMixMaterialData`
