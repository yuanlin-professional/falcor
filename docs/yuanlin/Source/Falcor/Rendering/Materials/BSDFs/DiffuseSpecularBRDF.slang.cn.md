# DiffuseSpecularBRDF.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/BSDFs/DiffuseSpecularBRDF.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials/BSDFs

## 功能概述

实现漫反射/镜面反射组合 BRDF，使用简化 Disney 漫反射叶瓣加各向同性 GGX 镜面叶瓣。主要用作 BRDF 近似拟合的目标函数，为 MERL 等实测材质提供重要性采样。

## 结构体与接口

### `DiffuseSpecularBRDF`

- **实现**: `IBSDF`

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `diffuse` | `float3` | 漫反射反照率 |
| `specular` | `float3` | 镜面反照率 |
| `roughness` | `float` | 线性粗糙度 |

#### 采样策略

按漫反射/镜面权重的亮度比例进行多叶瓣重要性采样（MIS）。漫反射使用余弦半球采样，镜面使用 GGX VNDF 采样。

## 依赖关系

### import / export
- `Rendering.Materials.IBSDF`（导出）
- `Scene.Material.DiffuseSpecularData`（导出）
- `Rendering.Materials.IsotropicGGX`
- `Rendering.Materials.Fresnel`
