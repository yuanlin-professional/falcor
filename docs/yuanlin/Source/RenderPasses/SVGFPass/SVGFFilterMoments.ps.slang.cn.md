# SVGFFilterMoments.ps.slang 源码文档

> 路径: `Source/RenderPasses/SVGFPass/SVGFFilterMoments.ps.slang`
> 类型: Slang 像素着色器
> 模块: RenderPasses/SVGFPass

## 功能概述

实现 SVGF 的矩滤波阶段。当时间历史长度不足（< 4 帧）时，使用 7x7 空间跨双边滤波计算光照的一阶和二阶矩，并从中估计方差。当历史充足时直接传递数据不做处理。对短历史的方差进行增强（乘以 4/h），以在初始帧提供更保守的估计。

## 函数

| 函数 | 说明 |
|------|------|
| `main(FullScreenPassVsOut)` | 矩滤波入口。历史不足时空间滤波估计方差，历史充足时直接传递 |

## 依赖关系 / import

- `Utils.Math.MathHelpers` — 数学工具
- `Utils.Color.ColorHelpers` — 亮度计算
- `SVGFCommon` — 公共模块
