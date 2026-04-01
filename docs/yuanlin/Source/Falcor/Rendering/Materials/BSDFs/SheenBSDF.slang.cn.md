# SheenBSDF.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/BSDFs/SheenBSDF.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Materials/BSDFs

## 功能概述

实现 Estevez & Kulla 2017 的 Sheen BSDF（"Production Friendly Microfacet Sheen BRDF"）。未反射的能量以 Dirac 脉冲透射。包含预拟合的方向性反照率查找表用于能量守恒计算。

## 结构体与接口

### `SheenBSDF`

- **实现**: `IBSDF`

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `color` | `float3` | Sheen 反射率 |
| `roughness` | `float` | 表面粗糙度（最大 1.0） |

#### 关键函数

| 函数签名 | 说明 |
|----------|------|
| `SheenD(NoH)` | Sheen NDF |
| `L(x)` | Lambda 函数拟合 |
| `G(NdotO, NdotI)` | 完整可见性函数（含终结器软化） |
| `evalAlbedo(wi, lobetype)` | 使用预拟合系数表计算反照率 |

## 依赖关系

### import / export
- `Rendering.Materials.IBSDF`（导出）
