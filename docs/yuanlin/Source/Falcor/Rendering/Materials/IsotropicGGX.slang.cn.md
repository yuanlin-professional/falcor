# IsotropicGGX.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/IsotropicGGX.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

提供各向同性 GGX（Trowbridge-Reitz）法线分布函数的完整工具集，包括 NDF 求值、NDF/VNDF 采样、Smith 遮蔽函数（可分离和相关形式）、Lambda 函数，以及预积分镜面 BRDF 近似。

## 函数

| 函数签名 | 说明 |
|----------|------|
| `evalNdfGGX(alpha, cosTheta)` | 求值 GGX 法线分布函数 D(h) |
| `evalPdfGGX_NDF(alpha, cosTheta)` | NDF 采样的 PDF: D(h)*cosTheta |
| `sampleGGX_NDF(alpha, u, pdf)` | Walter 2007 方法采样 GGX NDF |
| `evalPdfGGX_VNDF(alpha, wi, h)` | VNDF 采样的 PDF |
| `sampleGGX_VNDF(alpha, wi, u, pdf)` | Dupuy & Benyoub 2023 球帽方法采样 VNDF |
| `evalG1GGX(alphaSqr, cosTheta)` | Smith G1 遮蔽函数 |
| `evalLambdaGGX(alphaSqr, cosTheta)` | Smith Lambda 函数 |
| `evalMaskingSmithGGXSeparable(alpha, cosThetaI, cosThetaO)` | 可分离形式遮蔽-阴影函数 |
| `evalMaskingSmithGGXCorrelated(alpha, cosThetaI, cosThetaO)` | 高度相关形式遮蔽-阴影函数 |
| `approxSpecularIntegralGGX(specRefl, alpha, cosTheta)` | 预积分镜面 BRDF 近似 |

## 依赖关系

### include
- `Utils/Math/MathConstants.slangh`
- `BSDFConfig.slangh`
