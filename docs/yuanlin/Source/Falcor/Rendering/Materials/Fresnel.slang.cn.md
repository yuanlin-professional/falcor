# Fresnel.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/Fresnel.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

提供多种 Fresnel 项计算函数的集合，包括 Schlick 近似、电介质精确 Fresnel 方程、导体 Fresnel 方程以及反射率到折射率（IOR）的转换。所有函数均支持可微分。

## 函数

| 函数签名 | 说明 |
|----------|------|
| `evalFresnelSchlick(float3 f0, float3 f90, float cosTheta)` | Schlick 近似 Fresnel 项（float3 版本） |
| `evalFresnelSchlick(float f0, float f90, float cosTheta)` | Schlick 近似 Fresnel 项（标量版本） |
| `evalFresnelGeneralizedSchlick(f0, f90, exponent, cosTheta)` | 广义 Schlick 近似，可自定义指数 |
| `evalFresnelDielectric(float eta, float cosThetaI, out float cosThetaT)` | 电介质精确 Fresnel 方程（含折射角输出） |
| `evalFresnelDielectric(float eta, float cosThetaI)` | 电介质精确 Fresnel 方程（简化版） |
| `evalFresnelConductor(float eta, float k, float cosThetaI)` | 导体 Fresnel 方程（标量） |
| `evalFresnelConductor(float3 eta, float3 k, float cosThetaI)` | 导体 Fresnel 方程（3 波长） |
| `reflectanceToIOR(float3 reflectance)` | 从法线入射反射率计算折射率 |

## 依赖关系

### import
- `Utils.Color.ColorHelpers`
