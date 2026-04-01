# OrenNayarBRDF.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/BSDFs/OrenNayarBRDF.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials/BSDFs

## 功能概述

实现 Oren-Nayar 反射模型（"Generalization of Lambert's Reflectance Model"），适用于回溯反射表面。roughness=0 时退化为 Lambert 模型。使用余弦加权半球采样（对高回溯反射性可能效率较低）。

## 结构体与接口

### `OrenNayarBRDF`

- **实现**: `IBSDF`

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `albedo` | `float3` | 漫反射反照率 |
| `roughness` | `float` | 粗糙度 |

## 依赖关系

### import / export
- `Rendering.Materials.IBSDF`（导出）
