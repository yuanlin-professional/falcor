# ColorMap.slang 源码文档

> 路径: `Source/Falcor/Utils/Color/ColorMap.slang`
> 类型: Slang 着色器文件
> 模块: Utils/Color

## 功能概述

本文件提供了一组将标量值映射为 RGB 颜色的可视化辅助函数 (Color Map)。用于将 [0,1] 范围的标量数据映射为连续的 RGB 颜色,适合热力图、深度图等科学可视化场景。包含灰度、Jet、Viridis、Plasma、Magma、Inferno 六种常用色彩映射方案。

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float3 colormapGray(float x)` | 将标量映射为灰度 RGB 值。输入超出 [0,1] 范围时截断。 |
| `float3 colormapJet(float x)` | 将标量映射为 Matlab 经典 "jet" 色彩方案。蓝-青-绿-黄-红渐变。 |
| `float3 colormapViridis(float x)` | 将标量映射为 "viridis" 色彩方案。感知均匀、色盲友好。 |
| `float3 colormapPlasma(float x)` | 将标量映射为 "plasma" 色彩方案。紫-橙-黄渐变。 |
| `float3 colormapMagma(float x)` | 将标量映射为 "magma" 色彩方案。黑-紫-粉-白渐变。 |
| `float3 colormapInferno(float x)` | 将标量映射为 "inferno" 色彩方案。黑-红-黄-白渐变。 |

## 依赖关系

### import / include
- 无外部依赖 (自包含文件)

### 被以下文件引用
- 可被任何需要进行标量数据可视化的着色器 import

## 实现细节

- `colormapJet` 使用紧凑的解析公式实现,与 Matlab 的 `jet.m` 输出匹配。
- `colormapViridis`、`colormapPlasma`、`colormapMagma`、`colormapInferno` 基于 6 阶多项式拟合实现,系数来源于 Shadertoy (https://www.shadertoy.com/view/WlfXRN),原始数据取自 BIDS colormap 项目 (https://github.com/BIDS/colormap/blob/master/colormaps.py)。
- 所有函数均使用 `saturate` 对输入值和输出值进行 [0,1] 范围截断,确保结果始终为有效颜色。
