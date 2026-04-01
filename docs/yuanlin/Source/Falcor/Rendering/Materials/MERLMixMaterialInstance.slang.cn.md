# MERLMixMaterialInstance.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/MERLMixMaterialInstance.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

MERLMix 材质实例，支持带字节偏移的 MERL BRDF 数据查找。使用拟合 BRDF 进行采样，实际 MERL 数据进行求值。还提供了额外的 `ExtraBSDFProperties` 用于高级降噪等用途。

## 结构体与接口

### `MERLMixMaterialInstance`

- **继承**: `MaterialInstanceBase`, `IMaterialInstance`

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `byteOffset` | `uint` | BRDF 数据缓冲区的字节偏移 |
| `brdfIndex` | `uint` | 当前 BRDF 索引 |
| `fittedBrdf` | `DiffuseSpecularBRDF` | 拟合 BRDF |

## 依赖关系

### import / export
- `Scene.Material.DiffuseSpecularData`（导出）
- `Rendering.Materials.IMaterialInstance`（导出）
- `Rendering.Materials.BSDFs.DiffuseSpecularBRDF`
- `Rendering.Materials.MERLCommon`
