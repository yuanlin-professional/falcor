# InvalidPixelDetection.ps.slang 源码文档

> 路径: `Source/RenderPasses/DebugPasses/InvalidPixelDetectionPass/InvalidPixelDetection.ps.slang`
> 类型: 像素着色器
> 模块: RenderPasses/DebugPasses/InvalidPixelDetectionPass

## 功能概述

此着色器实现了无效像素检测的 GPU 逻辑。对每个像素，检查其四个通道是否包含 NaN 或 Inf 值，并输出相应的标记颜色。

## 结构体与接口

### 资源绑定

| 名称 | 类型 | 说明 |
|------|------|------|
| `gTexture` | `Texture2D<float4>` | 待检测的输入纹理 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float4 main(float2 texC)` | 像素着色器入口：NaN -> 红色(1,0,0,1)，Inf -> 绿色(0,1,0,1)，正常 -> 黑色(0,0,0,1) |

## 依赖关系 / import

- 无外部依赖

## 实现细节

着色器使用 `isnan()` 和 `isinf()` 内建函数检查每个像素的所有 4 个通道（使用 `any()` 聚合）。优先检测 NaN（红色），其次检测 Inf（绿色）。
