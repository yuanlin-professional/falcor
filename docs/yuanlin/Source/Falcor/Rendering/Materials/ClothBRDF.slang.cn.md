# ClothBRDF.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/ClothBRDF.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

实现布料 BRDF 模型，结合了 Estevez & Kulla 2017 的 Sheen 法线分布函数和 Neubelt & Pettineo 2013 的微表面可见性函数，参考 Google Filament 引擎的实现。

## 结构体与接口

### `ClothBRDF`

- **实现**: `IBSDF`
- **职责**: 布料材质的双向散射分布函数，包含 Sheen 高光和 Lambert 漫反射

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `roughness` | `float` | 线性粗糙度 |
| `f0` | `float3` | 法线入射时的镜面反射率 |
| `diffuseColor` | `float3` | 漫反射反照率 |
| `subsurfaceColor` | `float3` | 次表面颜色 |

#### 关键函数

| 函数签名 | 说明 |
|----------|------|
| `eval(wi, wo, sg, bc)` | 求值 BSDF，返回 f(wi,wo)*cos(theta_o) |
| `sample(wi, wo, pdf, weight, lobeType, sg, bc)` | 余弦加权半球采样 |
| `evalWeight(wi, wo)` | 计算 f(wi,wo)*pi |
| `D_Sheen(r, NoH)` | Sheen 法线分布函数 |
| `V_Neubelt(NoI, NoO)` | Neubelt 可见性函数 |

## 依赖关系

### import / export
- `Rendering.Materials.IBSDF`（导出）
- `Scene.ShadingData`
- `Utils.Math.MathHelpers`
