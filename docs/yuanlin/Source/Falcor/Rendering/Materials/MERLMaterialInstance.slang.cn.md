# MERLMaterialInstance.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/MERLMaterialInstance.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

MERL 实测材质的 BSDF 实例。使用拟合的漫反射+镜面 BRDF 进行重要性采样，但使用实际 MERL 数据进行求值。

## 结构体与接口

### `MERLMaterialInstance`

- **继承**: `MaterialInstanceBase`, `IMaterialInstance`

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `sf` | `ShadingFrame` | 世界空间着色帧 |
| `bufferID` | `uint` | BRDF 数据缓冲区 ID |
| `albedo` | `float3` | 近似反照率 |
| `fittedBrdf` | `DiffuseSpecularBRDF` | 拟合 BRDF（用于采样） |

## 依赖关系

### import / export
- `Rendering.Materials.IMaterialInstance`（导出）
- `Rendering.Materials.BSDFs.DiffuseSpecularBRDF`
- `Rendering.Materials.MERLCommon`
