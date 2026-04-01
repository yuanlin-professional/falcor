# HairMaterialInstance.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/HairMaterialInstance.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

Falcor 标准毛发/毛皮材质实例，使用 Chiang et al. 2016 散射函数。通过编译期宏 `BCSDF` 可切换为 Lambert 漫反射用于调试。报告漫反射 + 漫透射叶瓣类型。

## 结构体与接口

### `HairMaterialInstance`

- **继承**: `MaterialInstanceBase`, `IMaterialInstance`

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `sf` | `ShadingFrame` | 世界空间着色帧 |
| `data` | `HairChiang16Data` | Chiang16 BSDF 参数 |

#### 关键函数

| 函数签名 | 说明 |
|----------|------|
| `eval(sd, wo, sg)` | 求值材质实例 |
| `sample(sd, sg, result, ...)` | 采样出射方向 |
| `getProperties(sd)` | 返回属性（粗糙度约 0.5） |
| `getLobeTypes(sd)` | 返回漫反射+漫透射 |

## 依赖关系

### import / export
- `Rendering.Materials.IMaterialInstance`（导出）
- `Rendering.Materials.HairChiang16`（导出）
- `BCSDFConfig.slangh`
