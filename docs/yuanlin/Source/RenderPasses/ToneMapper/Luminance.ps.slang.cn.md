# Luminance.ps.slang 源码文档

> 路径: `Source/RenderPasses/ToneMapper/Luminance.ps.slang`
> 类型: 像素着色器 (Pixel Shader)
> 模块: RenderPasses/ToneMapper

## 功能概述

该像素着色器计算输入颜色纹理每个像素的对数亮度值。用于色调映射器的自动曝光功能，输出到带 mipmap 的帧缓冲区，通过读取最高 mip 级别可获得场景平均对数亮度。

## 结构体与接口

### 资源绑定

| 名称 | 类型 | 说明 |
|------|------|------|
| `gColorSampler` | `SamplerState` | 颜色纹理采样器（线性过滤） |
| `gColorTex` | `Texture2D` | 输入颜色纹理 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float luminance(float3 color)` | 使用 Rec.601 亮度系数（0.299, 0.587, 0.114）计算亮度 |
| `float4 main(float2 texC: TEXCOORD) : SV_TARGET0` | 主入口，采样颜色、计算对数亮度并输出 |

## 依赖关系/import

无外部依赖。

## 实现细节

1. **亮度计算**: 使用 ITU-R BT.601 亮度系数 `dot(color, float3(0.299, 0.587, 0.114))`。

2. **对数变换**: 输出 `log2(max(0.0001, luminance))`，使用 0.0001 作为最小值防止对数函数的奇异点。

3. **输出格式**: 仅使用 R 通道输出对数亮度值，其余通道为 0 和 1。输出的 FBO 会自动生成 mipmap，通过 mip 链的平均操作得到场景平均亮度。
