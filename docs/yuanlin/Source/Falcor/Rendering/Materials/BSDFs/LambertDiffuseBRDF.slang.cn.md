# LambertDiffuseBRDF.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/BSDFs/LambertDiffuseBRDF.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials/BSDFs

## 功能概述

实现 Lambert 漫反射 BRDF: f_r(wi, wo) = albedo / pi。最简单的漫反射模型，支持可微分渲染。

## 结构体与接口

### `LambertDiffuseBRDF`

- **实现**: `IBSDF`, `IDifferentiable`
- **采样**: 余弦加权半球采样，权重直接为 albedo

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `albedo` | `float3` | 漫反射反照率 |

## 依赖关系

### import / export
- `Rendering.Materials.IBSDF`（导出）
