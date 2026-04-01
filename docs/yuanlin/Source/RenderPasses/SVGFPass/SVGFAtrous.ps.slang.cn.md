# SVGFAtrous.ps.slang 源码文档

> 路径: `Source/RenderPasses/SVGFPass/SVGFAtrous.ps.slang`
> 类型: Slang 像素着色器
> 模块: RenderPasses/SVGFPass

## 功能概述

实现 SVGF 的 A-trous 小波分解滤波。使用 5x5 跨双边滤波核对光照进行空间滤波，利用深度、法线和亮度作为边缘停止函数保护几何边缘。支持可变步长（通过 `gStepSize` 控制），在多次迭代中逐步增大采样范围。方差通过 3x3 高斯模糊进行预滤波，且方差的重归一化使用权重的平方和。

## 函数

| 函数 | 说明 |
|------|------|
| `computeVarianceCenter(int2)` | 计算中心像素的 3x3 高斯模糊方差 |
| `main(FullScreenPassVsOut)` | A-trous 滤波主入口，对 5x5 邻域应用跨双边滤波 |

## 依赖关系 / import

- `Utils.Math.MathHelpers` — 数学工具
- `Utils.Color.ColorHelpers` — 亮度计算
- `SVGFCommon` — 公共权重计算函数
