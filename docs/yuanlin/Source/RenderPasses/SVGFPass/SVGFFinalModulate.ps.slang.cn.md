# SVGFFinalModulate.ps.slang 源码文档

> 路径: `Source/RenderPasses/SVGFPass/SVGFFinalModulate.ps.slang`
> 类型: Slang 像素着色器
> 模块: RenderPasses/SVGFPass

## 功能概述

SVGF 管线的最终调制阶段。将滤波后的光照与反照率相乘，再加上发射颜色，得到最终的降噪输出图像。公式为：`output = albedo * illumination + emission`。

## 函数

| 函数 | 说明 |
|------|------|
| `main(FullScreenPassVsOut)` | 最终调制：反照率 * 滤波光照 + 发射 |

## 依赖关系 / import

- `SVGFCommon` — 公共结构体定义
