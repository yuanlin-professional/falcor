# StandardBSDF.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/BSDFs/StandardBSDF.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials/BSDFs

## 功能概述

Falcor 标准材质的混合 BSDF 实现。由漫反射 BRDF + 漫透射 BTDF + 镜面反射 BRDF + 镜面透射 BSDF 四个叶瓣线性组合而成。漫反射模型可通过编译期宏选择（Lambert/Disney/Frostbite）。

## 结构体与接口

### `StandardBSDFData`

- **实现**: `IDifferentiable`
- **职责**: 标准 BSDF 的参数集合

| 成员 | 类型 | 说明 |
|------|------|------|
| `diffuse` | `float3` | 漫反射反照率 |
| `specular` | `float3` | 镜面反照率 |
| `roughness` | `float` | 原始粗糙度 |
| `metallic` | `float` | 金属度 |
| `eta` | `float` | 相对折射率 |
| `transmission` | `float3` | 透射颜色 |
| `diffuseTransmission` | `float` | 漫反射/漫透射混合 |
| `specularTransmission` | `float` | 电介质 BRDF/镜面 BSDF 混合 |

### `StandardBSDF`

- **实现**: `IBSDF`, `IDifferentiable`
- **职责**: 组合四个子叶瓣的混合 BSDF

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `diffuseReflection` | 漫反射 BRDF | 根据 `DiffuseBrdf` 宏选择具体类型 |
| `diffuseTransmission` | `LambertDiffuseBTDF` | 漫透射 |
| `specularReflection` | `SpecularMicrofacetBRDF` | 镜面反射 |
| `specularTransmission` | `SpecularMicrofacetBSDF` | 镜面透射 |
| `pDiffuseReflection` 等 | `float` | 各叶瓣采样概率 |

#### 关键常量

| 常量 | 值 | 说明 |
|------|-----|------|
| `kMinGGXAlpha` | 0.0064 | GGX alpha 下限，低于此值使用 Delta 事件 |

## 依赖关系

### import / export
- `Rendering.Materials.IBSDF`（导出）
- `Rendering.Materials.BSDFs.LambertDiffuseBRDF` / `DisneyDiffuseBRDF` / `FrostbiteDiffuseBRDF`（编译期选择）
- `Rendering.Materials.BSDFs.LambertDiffuseBTDF`
- `Rendering.Materials.BSDFs.SpecularMicrofacet`
- `Rendering.Materials.Fresnel`
