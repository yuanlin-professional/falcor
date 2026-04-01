# DisneyDiffuseBRDF.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/BSDFs/DisneyDiffuseBRDF.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials/BSDFs

## 功能概述

实现 Disney 漫反射 BRDF，基于 Burley 2012 的模型。使用 Schlick Fresnel 近似模拟掠射角处反射率增强效果。支持可微分渲染。

## 结构体与接口

### `DisneyDiffuseBRDF`

- **实现**: `IBSDF`, `IDifferentiable`

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `albedo` | `float3` | 漫反射反照率 |
| `roughness` | `float` | 重映射前的粗糙度 |

#### 采样
- 余弦加权半球采样
- 叶瓣类型: `DiffuseReflection`

## 依赖关系

### import / export
- `Rendering.Materials.IBSDF`（导出）
- `Rendering.Materials.Fresnel`
