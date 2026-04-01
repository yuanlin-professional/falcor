# ClothMaterial.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/ClothMaterial.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

实现简单布料材质，负责模式生成（pattern generation）过程：从纹理采样材质参数并创建 `ClothMaterialInstance`。

## 结构体与接口

### `ClothMaterial`

- **继承**: `MaterialBase`, `IMaterial`
- **职责**: 布料材质定义，采样基础颜色/高光纹理，计算 Fresnel F0，处理法线贴图

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `data` | `BasicMaterialData` | 基础材质数据 |

#### 关键函数

| 函数签名 | 说明 |
|----------|------|
| `setupMaterialInstance(ms, sd, lod, hints)` | 创建布料材质实例 |
| `setupDiffMaterialInstance(...)` | 可微分版本（当前直接委托给非可微版本） |

## 依赖关系

### import / export
- `Rendering.Materials.IMaterial`（导出）
- `Rendering.Materials.ClothMaterialInstance`（导出）
- `Scene.Material.BasicMaterialData`
- `Scene.Material.ShadingUtils`
- `Scene.Scene`
