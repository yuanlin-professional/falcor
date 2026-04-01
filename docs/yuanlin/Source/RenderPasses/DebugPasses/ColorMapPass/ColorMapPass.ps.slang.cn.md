# ColorMapPass.ps.slang 源码文档

> 路径: `Source/RenderPasses/DebugPasses/ColorMapPass/ColorMapPass.ps.slang`
> 类型: 像素着色器
> 模块: RenderPasses/DebugPasses/ColorMapPass

## 功能概述

此着色器实现了颜色映射的 GPU 逻辑。从输入纹理的指定通道读取原始值，归一化到 [0,1] 范围后，应用选定的颜色映射方案输出伪彩色。

## 结构体与接口

### 编译时宏定义

| 宏 | 说明 |
|----|------|
| `_FORMAT` | 输入纹理格式：`FORMAT_FLOAT`(0)、`FORMAT_UINT`(1)、`FORMAT_SINT`(2) |
| `_COLOR_MAP` | 颜色映射方案索引 |
| `_CHANNEL` | 读取的通道索引 |

### 常量缓冲区 `StaticCB`

| 字段 | 类型 | 说明 |
|------|------|------|
| `gParams` | `ColorMapParams` | 包含 minValue 和 maxValue |

### 资源绑定

| 名称 | 类型 | 说明 |
|------|------|------|
| `gTexture` | `Texture2D<float4/uint4/int4>` | 输入纹理（类型由 `_FORMAT` 决定） |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float3 applyColorMap(float value)` | 根据 `kColorMap` 选择对应的颜色映射函数 |
| `float4 main(float2 texC, float4 pos)` | 像素着色器入口：读取通道值 -> 归一化 -> 颜色映射 |

## 依赖关系 / import

- `RenderPasses.DebugPasses.ColorMapPass.ColorMapParams` — 参数结构体定义
- `Utils.Color.ColorMap` — 提供各种颜色映射函数（colormapGray、colormapJet 等）

## 实现细节

支持的颜色映射方案：Grey（灰度）、Jet（彩虹）、Viridis、Plasma、Magma、Inferno。归一化公式为 `saturate((value - minValue) / (maxValue - minValue))`，当 min == max 时归一化为 0。
