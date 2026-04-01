# FrostbiteDiffuseBRDF.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/BSDFs/FrostbiteDiffuseBRDF.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials/BSDFs

## 功能概述

实现 Frostbite 漫反射 BRDF，在 Disney 漫反射基础上增加了临时能量守恒归一化因子（energyFactor = lerp(1, 1/1.51, roughness)）。支持可微分渲染。

## 结构体与接口

### `FrostbiteDiffuseBRDF`

- **实现**: `IBSDF`, `IDifferentiable`

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `albedo` | `float3` | 漫反射反照率 |
| `roughness` | `float` | 重映射前的粗糙度 |

## 依赖关系

### import / export
- `Rendering.Materials.IBSDF`（导出）
- `Rendering.Materials.Fresnel`
