# PhaseFunction.slang 源码文档

> 路径: `Source/Falcor/Rendering/Volumes/PhaseFunction.slang`
> 类型: Slang 着色器模块
> 模块: Rendering/Volumes

## 功能概述

本文件实现了多种相位函数，均实现 `IPhaseFunction` 接口。包括：
1. **NullPhaseFunction** — 空碰撞相位函数（前向传播）
2. **IsotropicPhaseFunction** — 各向同性相位函数
3. **HenyeyGreensteinPhaseFunction** — Henyey-Greenstein (HG) 相位函数
4. **DualHenyeyGreensteinPhaseFunction** — 双波瓣 HG 相位函数

此外还提供了 HG 相位函数的独立评估辅助函数 `evalHenyeyGreenstein()`。

## 结构体与接口

### `NullPhaseFunction`

空碰撞相位函数，出射方向始终为入射方向的反方向（`wo = -wi`）。

| 方法 | 说明 |
|------|------|
| `eval(wi, wo)` | 始终返回 0 |
| `sample<S>(...)` | `wo = -wi`，`pdf = 1`，`weight = 1` |
| `evalPdf(wi, wo)` | 始终返回 0 |

### `IsotropicPhaseFunction`

各向同性相位函数：f_p = 1/(4*PI)

| 方法 | 说明 |
|------|------|
| `eval(wi, wo)` | 返回 `1/(4*PI)` |
| `sample<S>(...)` | 均匀球面采样 |
| `evalPdf(wi, wo)` | 返回 `1/(4*PI)` |

### `HenyeyGreensteinPhaseFunction`

各向异性 Henyey-Greenstein 相位函数。

| 成员 | 类型 | 说明 |
|------|------|------|
| `g` | `float` | 不对称参数，g > 0 前向散射，g < 0 后向散射 |

| 方法 | 说明 |
|------|------|
| `eval(wi, wo)` | 评估 HG 相位函数 |
| `sample<S>(...)` | 逆 CDF 采样 HG 分布 |
| `evalPdf(wi, wo)` | 等同于 eval（HG 的归一化 PDF 等于函数值） |

### `DualHenyeyGreensteinPhaseFunction`

双波瓣 HG 相位函数，由两个 HG 波瓣加权混合。支持每通道独立的不对称参数（`float3`）。

| 成员 | 类型 | 说明 |
|------|------|------|
| `meancosine[2]` | `float3[2]` | 两个波瓣的平均余弦值（每通道独立） |
| `w0` | `float3` | 第一个波瓣的权重（每通道独立） |

| 方法 | 说明 |
|------|------|
| `eval(wi, wo)` | 两个 HG 评估的加权和 |
| `sample<S>(...)` | 按平均权重选择波瓣，使用通道平均 g 值采样 |
| `evalPdf(wi, wo)` | 两个 HG PDF 的加权和 |

## 函数

### 独立辅助函数

| 函数 | 说明 |
|------|------|
| `evalHenyeyGreenstein(cosTheta, g: float) -> float` | 标量版 HG 评估 |
| `evalHenyeyGreenstein(cosTheta, g: float3) -> float3` | 向量版 HG 评估（每通道独立 g） |

HG 公式: `(1 - g^2) / (4*PI * (1 + g^2 + 2*g*cosTheta)^(3/2))`

## 依赖关系 / import

- `Utils/Math/MathConstants.slangh`
- `IPhaseFunction`（`__exported import`，导出接口）
- `Utils.Math.MathHelpers`（`perp_stark()` 等）

## 实现细节

- **HG 采样**: 当 `|g| < 1e-3` 时退化为各向同性采样，避免数值不稳定
- **切线空间构建**: 使用入射方向的反方向作为主轴，通过 `perp_stark()` 构建正交基
- **双波瓣 HG**: 采样时取三通道 g 值的平均进行方向采样，然后通过权重补偿保持无偏。这是一种近似策略，对各向异性 RGB 散射参数不是最优选择
- 约定：入射方向和出射方向均指向远离散射点（与 PBRT 约定一致），因此 HG 公式中使用 `+2*g*cosTheta`
