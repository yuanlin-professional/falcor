# IMaterialInstance.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/IMaterialInstance.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

定义材质实例接口 `IMaterialInstance` 及相关数据结构。材质实例包含在着色点求值后的所有材质属性，支持 BSDF 的求值、采样和属性查询。所有计算在世界坐标系中进行。

## 结构体与接口

### `IMaterialInstance`（接口）

- **anyValueSize**: `FALCOR_MATERIAL_INSTANCE_SIZE`（编译期确定）

| 方法签名 | 说明 |
|----------|------|
| `eval(sd, wo, sg)` | 求值 f(wi,wo)*dot(wo,n) |
| `evalAD(diffData, sd, wo, sg)` | 可微分求值 |
| `sample(sd, sg, result, useImportanceSampling)` | 采样出射方向 |
| `evalPdf(sd, wo, useImportanceSampling)` | 计算方向 PDF |
| `getProperties(sd)` | 返回 BSDF 属性 |
| `getLobeTypes(sd)` | 返回可用叶瓣类型集合 |
| `hasVolumeProperties()` | 是否有体积属性 |
| `getVolumeProperties()` | 获取纹理化体积属性 |

### `BSDFSample`

采样结果，包含出射方向 `wo`、PDF `pdf`、权重 `weight` 和叶瓣类型 `lobeType`。

### `BSDFProperties`

BSDF 属性，包含发射辐射度、粗糙度、引导法线、标志位，以及四种方向-半球反照率近似值。

### `MaterialInstanceBase`

材质实例基类，提供体积属性的默认实现。

## 依赖关系

### export
- `Scene.ShadingData`
- `Scene.Material.ShadingUtils`
- `Rendering.Materials.IBSDF`
- `Rendering.Materials.LobeType`
- `Utils.Sampling.SampleGeneratorInterface`
- `DiffRendering.DiffMaterialData`
- `MaterialInstanceHints`
