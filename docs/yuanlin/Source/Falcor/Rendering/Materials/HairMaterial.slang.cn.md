# HairMaterial.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/HairMaterial.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

Falcor 标准毛发/毛皮材质的实现，负责从纹理采样材质参数（基础颜色、粗糙度、倾斜角）并创建 `HairMaterialInstance`。注意背面命中不翻转法线，因为 BSDF 自身处理双面透射。

## 结构体与接口

### `HairMaterial`

- **继承**: `MaterialBase`, `IMaterial`
- **关联类型**: `HairMaterialInstance`

#### 关键函数

| 函数签名 | 说明 |
|----------|------|
| `setupMaterialInstance(ms, sd, lod, hints)` | 创建毛发材质实例 |

## 依赖关系

### import / export
- `Rendering.Materials.IMaterial`（导出）
- `Rendering.Materials.HairMaterialInstance`（导出）
- `Scene.Material.BasicMaterialData`
