# HairChiang16.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/HairChiang16.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

实现 Chiang et al. 2016 的毛发 BCSDF 模型（"A Practical and Controllable Hair and Fur Model for Production Path Tracing"），改编自 pbrt-v3。支持多阶散射事件的显式计算和重要性采样。

## 结构体与接口

### `HairChiang16Data`

- **职责**: 毛发 BSDF 的初始化参数

| 成员 | 类型 | 说明 |
|------|------|------|
| `baseColor` | `float3` | 基础颜色 |
| `betaM` / `betaN` | `float` | 纵向/方位角粗糙度 |
| `alpha` | `float` | 毛鳞片倾斜角 |
| `IoR` / `eta` | `float` | 折射率 / 相对折射率 |

### `HairChiang16`

- **实现**: `IBSDF`
- **职责**: 毛发散射函数的求值和采样，支持 R/TT/TRT 及更高阶散射项

#### 关键函数

| 函数签名 | 说明 |
|----------|------|
| `eval(wi, wo, sg, bc)` | 求值 BCSDF |
| `sample(wi, wo, pdf, weight, lobeType, sg, bc)` | 重要性采样 |
| `evalPdf(wi, wo, bc)` | 计算采样 PDF |
| `Ap(cosThetaI, T, ap)` | 计算各阶衰减函数 |
| `computeApPdf(cosThetaI, apPdf)` | 计算各阶衰减的离散 PDF |

### 辅助函数

| 函数 | 说明 |
|------|------|
| `Mp(...)` | 纵向散射函数 |
| `Np(...)` | 方位角散射函数 |
| `I0(x)` / `logI0(x)` | 修正贝塞尔函数及其对数 |
| `logistic` / `trimmedLogistic` | 截断 Logistic 分布 |
| `sigmaAFromColor(color, betaN)` | 从颜色映射到吸收系数 |

## 依赖关系

### import / export
- `Rendering.Materials.IBSDF`（导出）
- `Rendering.Materials.Fresnel`
- `Scene.ShadingData`
- `Utils.Math.MathHelpers`
- `Utils.Color.ColorHelpers`
