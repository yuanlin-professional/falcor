# SideBySide.ps.slang 源码文档

> 路径: `Source/RenderPasses/DebugPasses/SideBySidePass/SideBySide.ps.slang`
> 类型: 像素着色器
> 模块: RenderPasses/DebugPasses/SideBySidePass

## 功能概述

此着色器实现了并排图像对比的像素级逻辑。与分屏模式不同，左侧图像从 `gLeftBound` 偏移处开始采样，右侧图像则从分割线位置对应的偏移处采样，使得两幅图像各自显示不同区域的内容。

## 结构体与接口

### `SideBySide`

- **实现接口**: `ICalcPixelColor`
- **职责**: 提供并排对比模式下左右像素颜色的计算逻辑

| 方法 | 说明 |
|------|------|
| `float2x4 calcColors(uint2 pixelPos)` | 左侧：`gLeftInput[x + gLeftBound, y]`；右侧：`gRightInput[x - gSplitLocation + gLeftBound, y]` |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float4 main(float2 texC, float4 pos)` | 像素着色器入口 |

## 依赖关系 / import

- `RenderPasses/DebugPasses/Comparison.ps` — 提供 `ICalcPixelColor` 接口和 `compare` 函数
