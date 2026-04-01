# Comparison.ps.slang 源码文档

> 路径: `Source/RenderPasses/DebugPasses/Comparison.ps.slang`
> 类型: 像素着色器（公共库）
> 模块: RenderPasses/DebugPasses

## 功能概述

此着色器定义了图像对比渲染通道的公共逻辑。它提供了一个泛型 `compare` 函数，根据分割线位置选择显示左侧或右侧图像，并叠加分割线和可选的箭头指示器。具体的像素颜色计算通过 `ICalcPixelColor` 接口由子类着色器实现。

## 结构体与接口

### 常量缓冲区 `GlobalCB`

| 字段 | 类型 | 说明 |
|------|------|------|
| `gSplitLocation` | `int` | 分割线的 X 坐标（像素） |
| `gDividerSize` | `uint` | 分割线宽度 |
| `gDividerColor` | `float4` | 分割线颜色 |
| `gMousePosition` | `int2` | 鼠标当前位置 |
| `gDrawArrows` | `bool` | 是否绘制箭头 |
| `gLeftBound` | `uint` | 比较窗口的左边界偏移 |

### 接口 `ICalcPixelColor`

| 方法 | 说明 |
|------|------|
| `float2x4 calcColors(uint2 pixelPos)` | 计算给定像素位置的左右两侧颜色 |

### 资源绑定

| 名称 | 类型 | 说明 |
|------|------|------|
| `gLeftInput` | `Texture2D` | 左侧输入纹理 |
| `gRightInput` | `Texture2D` | 右侧输入纹理 |
| `gArrowTex` | `Texture2D<float>` | 箭头纹理（16x16 灰度） |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float4 compare<C : ICalcPixelColor>(float2, float4, C)` | 泛型对比函数：根据分割位置选择左/右颜色，叠加分割线和箭头 |

## 依赖关系 / import

- 无外部 import（独立着色器库）

## 实现细节

1. **泛型设计**: 使用 Slang 泛型和接口机制，`compare` 函数接受实现了 `ICalcPixelColor` 接口的类型参数，使得不同对比模式（分屏、并排等）可以复用公共逻辑。
2. **箭头绘制**: 使用一个 16x16 的灰度箭头纹理，在分割线两侧以鼠标位置为中心绘制左右箭头。右侧箭头通过水平翻转左侧箭头实现。
