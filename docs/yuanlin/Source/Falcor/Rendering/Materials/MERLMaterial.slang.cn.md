# MERLMaterial.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/MERLMaterial.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

实现基于 MERL 实测数据的材质。从预计算的反照率查找表中采样方向性反照率，创建 `MERLMaterialInstance`。

## 结构体与接口

### `MERLMaterial`

- **继承**: `MaterialBase`, `IMaterial`
- **关联类型**: `MERLMaterialInstance`

#### 关键函数

| 函数签名 | 说明 |
|----------|------|
| `setupMaterialInstance(ms, sd, lod, hints)` | 创建材质实例，采样反照率 LUT |

## 依赖关系

### import / export
- `Rendering.Materials.IMaterial`（导出）
- `Rendering.Materials.MERLMaterialInstance`（导出）
- `Scene.Material.MERLMaterialData`
