# StandardMaterialInstance.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/StandardMaterialInstance.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

Falcor 标准表面 BSDF 的材质实例实现。内部使用 `StandardBSDF` 进行求值和采样，支持可微分渲染和参考实现（余弦加权半球采样）的切换。

## 结构体与接口

### `StandardMaterialInstance`

- **继承**: `MaterialInstanceBase`, `IMaterialInstance`

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `sf` | `ShadingFrame` | 世界空间着色帧 |
| `data` | `StandardBSDFData` | BSDF 参数 |
| `emission` | `float3` | 入射方向的发射辐射度 |

#### BSDF 叶瓣
- Delta 反射（理想镜面反射）
- 镜面反射（GGX 微表面模型）
- 漫反射（Disney 漫反射 BRDF）
- Delta 透射（理想折射）
- 镜面透射（GGX 微表面模型）
- 漫透射

#### 关键函数

| 函数签名 | 说明 |
|----------|------|
| `eval(sd, wo, sg)` | 求值材质实例 |
| `evalAD(diffData, sd, wo, sg)` | 可微分求值 |
| `sample(sd, sg, result, useImportanceSampling)` | 采样（支持切换参考实现） |
| `getProperties(sd)` | 返回 BSDF 属性 |
| `hasVolumeProperties()` / `getVolumeProperties()` | 体积属性支持 |

## 依赖关系

### import / export
- `Rendering.Materials.IMaterialInstance`（导出）
- `Rendering.Materials.BSDFs.StandardBSDF`（导出）
- `DiffRendering.SceneGradients`（导出）
- `DiffRendering.GradientIOWrapper`（导出）
