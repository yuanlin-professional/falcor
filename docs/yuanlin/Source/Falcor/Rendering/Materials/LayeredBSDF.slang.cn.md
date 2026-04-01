# LayeredBSDF.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/LayeredBSDF.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

实现基于 Guo et al. "Position-Free Monte Carlo Simulation for Arbitrary Layered BSDFs" 的分层材质模型。允许将两个任意 BSDF 进行分层组合，中间可填充具有任意消光系数、反照率和平均散射余弦的介质。

## 结构体与接口

### `LayeredBSDF`

- **实现**: `IBSDF`
- **职责**: 通过蒙特卡洛随机游走在顶部和底部界面之间传输光线

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `top` | `IBSDF` | 顶层 BSDF |
| `bottom` | `IBSDF` | 底层 BSDF |
| `sigmaT` | `float` | 消光系数 |
| `g` | `float` | Henyey-Greenstein 相函数参数 |
| `albedo` | `float3` | 介质反照率 |
| `maxDepth` | `int` | 最大随机游走深度（默认 64） |

#### 关键函数

| 函数签名 | 说明 |
|----------|------|
| `eval(wi, wo, sg, bc)` | 通过随机游走概率性求值 |
| `sample(wi, wo, pdf, weight, lobeType, sg, bc)` | 随机游走采样 |
| `evalPdf(wi, wo, bc)` | 近似 PDF（仅单次反射叶瓣） |

## 依赖关系

### import / export
- `Rendering.Materials.IMaterialInstance`（导出）
- `Rendering.Materials.IBSDF`（导出）
- `Rendering.Materials.Fresnel`
- `Rendering.Volumes.PhaseFunction`
- `Utils.Color.ColorHelpers`
