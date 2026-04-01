# ClothMaterialInstance.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/ClothMaterialInstance.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

布料材质实例的实现，封装了 `ClothBRDF` 并在世界坐标系与局部坐标系之间进行方向转换。使用余弦加权半球采样作为参考实现。

## 结构体与接口

### `ClothMaterialInstance`

- **继承**: `MaterialInstanceBase`, `IMaterialInstance`
- **职责**: 在着色点求值、采样布料 BSDF

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `sf` | `ShadingFrame` | 世界空间着色帧 |
| `brdf` | `ClothBRDF` | 布料 BRDF 参数 |

#### 关键函数

| 函数签名 | 说明 |
|----------|------|
| `eval(sd, wo, sg)` | 求值材质实例 |
| `sample(sd, sg, result, useImportanceSampling)` | 采样（使用余弦半球采样） |
| `evalPdf(sd, wo, useImportanceSampling)` | 计算方向 PDF |
| `getProperties(sd)` | 返回 BSDF 属性 |
| `getLobeTypes(sd)` | 返回漫反射叶瓣类型 |

## 依赖关系

### import / export
- `Rendering.Materials.IMaterialInstance`（导出）
- `Rendering.Materials.ClothBRDF`（导出）
- `Utils.Math.MathHelpers`
