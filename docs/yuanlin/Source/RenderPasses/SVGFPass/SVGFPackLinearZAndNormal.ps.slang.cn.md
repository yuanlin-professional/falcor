# SVGFPackLinearZAndNormal.ps.slang 源码文档

> 路径: `Source/RenderPasses/SVGFPass/SVGFPackLinearZAndNormal.ps.slang`
> 类型: Slang 像素着色器
> 模块: RenderPasses/SVGFPass

## 功能概述

将线性 Z 缓冲区（包含线性 Z 值及其导数）和世界法线打包到一个 RGBA32Float 纹理中。XY 通道存储线性 Z 及其导数，ZW 通道存储八面体编码（Oct）的法线。这种打包简化了后续通道的采样，并便于复制到前一帧缓冲区以供下一帧重投影使用。

## 函数

| 函数 | 说明 |
|------|------|
| `main(FullScreenPassVsOut)` | 读取线性 Z 和法线，打包为 float4(linearZ, dLinearZ, octNormal.x, octNormal.y) |

## 依赖关系 / import

- `Utils.Math.MathHelpers` — 八面体编码函数
- `SVGFCommon` — 公共结构体
