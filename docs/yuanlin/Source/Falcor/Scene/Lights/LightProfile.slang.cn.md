# LightProfile.slang 源码文档

> 路径: `Source/Falcor/Scene/Lights/LightProfile.slang`
> 类型: Slang 着色器文件
> 模块: Scene/Lights

## 功能概述

GPU 端的光源配置文件（IES Profile）访问结构体。提供根据余弦角度查询光强分布的功能，用于实现非均匀光强分布的光源。

## 结构体与接口

### `LightProfile`

| 成员 | 类型 | 说明 |
|------|------|------|
| `fluxFactor` | `float` | 通量因子（配置文件的球面积分） |
| `texture` | `Texture2D` | 烘焙后的光强分布纹理 |
| `sampler` | `SamplerState` | 纹理采样器 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float getFluxFactor()` | 获取通量因子 |
| `float eval(float cosTheta)` | 根据余弦角度评估光强。将 cosTheta 转换为归一化的 theta/PI，然后采样纹理 |

## 依赖关系 / import

- `Utils/Math/MathConstants.slangh` — 数学常量（M_PI）
