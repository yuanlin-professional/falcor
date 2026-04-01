# AnisotropicGGX.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/AnisotropicGGX.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

实现各向异性 GGX（Trowbridge-Reitz）法线分布函数（NDF）的采样与求值。该类使用可见法线分布（VNDF）进行采样，相比传统 NDF 采样具有更低的方差。

## 结构体与接口

### `AnisotropicGGX`

- **实现**: `IDifferentiable`
- **职责**: 提供各向异性 GGX 微表面模型的核心数学运算，包括 NDF 求值、Smith 遮蔽函数、VNDF 采样等

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `alpha` | `float2` | 各向异性粗糙度参数（x 和 y 方向） |

#### 关键函数

| 函数签名 | 说明 |
|----------|------|
| `__init(float2 alpha)` | 构造函数，设置各向异性粗糙度 |
| `isSingular()` | 判断是否为奇异分布（近似理想镜面） |
| `getIsotropicRoughness()` | 返回各向同性等效粗糙度（取最大值） |
| `smithLambda(float3 v)` | 计算 Smith Lambda 函数，对方向对称 |
| `G1(float3 w)` | 计算单向 Smith 遮蔽函数 |
| `G2(float3 wo, float3 wi)` | 计算双向 Smith 遮蔽-阴影函数（反射用） |
| `evalNDF(float3 h)` | 计算法线分布函数 D(h) |
| `evalVNDF(float3 w, float3 h)` | 计算可见法线分布函数 D_wi(h) |
| `sample(float3 w, inout S sg)` | 采样从方向 w 可见的法线分布 |
| `evalPDF(float3 w, float3 h)` | 计算从 VNDF(w) 采样法线 h 的概率密度 |

## 依赖关系

### import
- `Utils.Math.MathHelpers`
- `Utils.Sampling.SampleGeneratorInterface`

### include
- `Utils/Math/MathConstants.slangh`
