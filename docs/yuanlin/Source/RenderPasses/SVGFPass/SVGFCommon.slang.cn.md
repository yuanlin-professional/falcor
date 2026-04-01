# SVGFCommon.slang 源码文档

> 路径: `Source/RenderPasses/SVGFPass/SVGFCommon.slang`
> 类型: Slang 着色器
> 模块: RenderPasses/SVGFPass

## 功能概述

SVGF 降噪通道的公共着色器模块，提供所有 SVGF 着色器共享的辅助函数和结构体定义。包含纹理尺寸获取、边缘停止权重计算以及全屏通道顶点着色器输出结构体。

## 结构体与接口

### `FullScreenPassVsOut`

| 成员 | 语义 | 说明 |
|------|------|------|
| `texC` | TEXCOORD | 纹理坐标 |
| `posH` | SV_POSITION / POSITION | 裁剪空间位置 |

## 函数

| 函数 | 说明 |
|------|------|
| `getTextureDims(Texture2D, uint)` | 获取纹理的宽高尺寸 |
| `computeWeight(depthCenter, depthP, phiDepth, normalCenter, normalP, phiNormal, lumCenter, lumP, phiIllum)` | 计算跨双边滤波权重，结合深度、法线和亮度三个边缘停止函数 |

## 依赖关系 / import

无外部 import（独立公共模块）。
