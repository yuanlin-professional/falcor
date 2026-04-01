# Microfacet.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/Microfacet.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials

## 功能概述

实现通用微表面法线分布函数，支持椭圆各向异性。需要通过具体的拉伸不变 NDF（如 `TrowbridgeReitzNDF`）进行特化。参考 Jonathan Dupuy 2015 博士论文。

## 结构体与接口

### `Microfacet`

- **职责**: 通用各向异性微表面分布的求值、采样和遮蔽函数计算

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `ndf` | `INDF` | 底层法线分布函数 |
| `alpha` | `float2` | 各向异性粗糙度参数 |
| `rho` | `float` | 椭圆各向异性轴编码 |

#### 关键函数

| 函数签名 | 说明 |
|----------|------|
| `__init(INDF, float)` | 各向同性初始化 |
| `__init(INDF, float2, float)` | 各向异性初始化（含旋转角） |
| `evalD(float3 wm)` | 求值 NDF D(wm) |
| `sampleFull(sg, pdf)` | 按 D(wm)*cos(theta) 采样 |
| `sampleVisible(wi, sg, pdf)` | 按可见法线分布采样 |
| `evalG1(float3 w)` | Smith G1 遮蔽函数 |
| `evalG2Separable(wi, wo)` | 可分离遮蔽-阴影函数 |
| `evalG2Correlated(wi, wo)` | 高度相关遮蔽-阴影函数 |
| `evalSigma(float3 dir)` | 投影面积 |
| `evalLambda(float3 dir)` | Smith Lambda 函数 |

## 依赖关系

### import / export
- `Rendering.Materials.NDF`（导出）
- `Utils.Math.MathHelpers`
