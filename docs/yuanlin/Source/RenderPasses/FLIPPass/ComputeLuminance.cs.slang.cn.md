# ComputeLuminance.cs.slang 源码文档

> 路径: `Source/RenderPasses/FLIPPass/ComputeLuminance.cs.slang`
> 类型: 计算着色器 (Compute Shader)
> 模块: RenderPasses/FLIPPass

## 功能概述

计算 RGB 图像每个像素的亮度值，用于 HDR-FLIP 的自动曝光参数推导。输出为一维浮点缓冲区，供 CPU 端排序计算中位数和最大值。

## 结构体与接口

### 输入/输出

| 资源 | 类型 | 说明 |
|------|------|------|
| `gInputImage` | `Texture2D<float4>` | 输入 RGB 图像 |
| `gOutputLuminance` | `RWBuffer<float>` | 输出亮度缓冲区 |

### 常量缓冲区 `PerFrameCB`

| 成员 | 类型 | 说明 |
|------|------|------|
| `gResolution` | `uint2` | 图像分辨率 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `[numthreads(16,16,1)] void computeLuminance(uint3)` | 计算着色器入口，逐像素计算亮度 |

## 依赖关系 / import

- `Utils.Color.ColorHelpers` — `luminance()` 函数

## 实现细节

- 亮度使用 Rec.709 系数计算：`Y = 0.2126*R + 0.7152*G + 0.0722*B`
- 输出按行优先线性索引存储：`index = x + width * y`
