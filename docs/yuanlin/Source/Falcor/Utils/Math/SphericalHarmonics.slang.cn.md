# SphericalHarmonics.slang 着色器文档

> 路径: `Source/Falcor/Utils/Math/SphericalHarmonics.slang`
> 类型: Slang 着色器
> 模块: Utils/Math

## 功能概述

提供球谐函数 (Spherical Harmonics, SH) 基函数的求值功能。支持 0-3 阶（共 16 个基函数），用于全局光照、辐照度图压缩等场景。

## 函数
| 函数签名 | 说明 |
|----------|------|
| `uint get_SH_index(int l, int m)` | 根据阶 l 和序 m 计算基函数的连续索引 |
| `float eval_SH(uint idx, float3 p)` | 按索引求值球谐基函数 Y_i（支持反向自动微分） |
| `float eval_SH(int l, int m, float3 p)` | 按阶/序求值球谐基函数 Y_l^m |

## 依赖关系
### import
- `Utils/Math/MathConstants.slangh`（通过 `#include`）
- `Utils.Math.MathHelpers`（通过 `import`）

## 实现细节

- 基函数使用标准实数球谐定义（非 Sloan 2008 的符号约定）。
- `eval_SH(uint idx, ...)` 标记为 `[BackwardDifferentiable]`，支持可微分渲染。
- 当前实现通过 switch-case 硬编码，仅支持最高 3 阶（16 个基函数）。
