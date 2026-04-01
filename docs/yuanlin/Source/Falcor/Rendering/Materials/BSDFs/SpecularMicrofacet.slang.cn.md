# SpecularMicrofacet.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/BSDFs/SpecularMicrofacet.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials/BSDFs

## 功能概述

实现基于微表面理论的镜面反射（BRDF）和镜面反射+透射（BSDF），使用 GGX 法线分布。支持 Delta 事件（理想镜面/折射）、VNDF 采样和可分离/相关 Smith 遮蔽函数。

## 结构体与接口

### `SpecularMicrofacetBRDF`

- **实现**: `IBSDF`, `IDifferentiable`
- **职责**: 仅反射的微表面 BRDF

| 成员 | 类型 | 说明 |
|------|------|------|
| `albedo` | `float3` | 镜面反照率 |
| `alpha` | `float` | GGX 宽度参数（0 表示 Delta） |
| `activeLobes` | `uint` | 活动叶瓣掩码 |

### `SpecularMicrofacetBSDF`

- **实现**: `IBSDF`, `IDifferentiable`
- **职责**: 反射+透射的微表面 BSDF

| 成员 | 类型 | 说明 |
|------|------|------|
| `transmissionAlbedo` | `float3` | 透射反照率 |
| `alpha` | `float` | GGX 宽度参数 |
| `eta` | `float` | 相对折射率 |
| `activeLobes` | `uint` | 活动叶瓣掩码 |

#### 叶瓣类型
- Delta/Specular Reflection + Delta/Specular Transmission

## 依赖关系

### import / export
- `Rendering.Materials.IBSDF`（导出）
- `Rendering.Materials.IsotropicGGX`
- `Rendering.Materials.Fresnel`
