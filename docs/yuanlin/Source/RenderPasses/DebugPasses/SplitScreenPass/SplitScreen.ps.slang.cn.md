# SplitScreen.ps.slang 源码文档

> 路径: `Source/RenderPasses/DebugPasses/SplitScreenPass/SplitScreen.ps.slang`
> 类型: 像素着色器
> 模块: RenderPasses/DebugPasses/SplitScreenPass

## 功能概述

此着色器实现了交互式分屏图像对比的像素级逻辑。它实现了 `ICalcPixelColor` 接口，直接从左右输入纹理的相同像素位置读取颜色数据，然后通过公共 `compare` 函数完成分屏合成。

## 结构体与接口

### `InteractiveImageComparison`

- **实现接口**: `ICalcPixelColor`
- **职责**: 提供分屏对比模式下左右像素颜色的计算逻辑

| 方法 | 说明 |
|------|------|
| `float2x4 calcColors(uint2 pixelPos)` | 从 `gLeftInput` 和 `gRightInput` 的相同位置采样 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float4 main(float2 texC, float4 pos)` | 像素着色器入口，创建 `InteractiveImageComparison` 并调用 `compare` |

## 依赖关系 / import

- `RenderPasses/DebugPasses/Comparison.ps` — 提供 `ICalcPixelColor` 接口和 `compare` 函数
