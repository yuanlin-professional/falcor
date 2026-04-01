# ErrorMeasurer.cs.slang 源码文档

> 路径: `Source/RenderPasses/ErrorMeasurePass/ErrorMeasurer.cs.slang`
> 类型: 计算着色器 (Compute Shader)
> 模块: RenderPasses/ErrorMeasurePass

## 功能概述

误差度量的计算着色器，逐像素计算源图像和参考图像之间的差异。支持 L1（绝对值差）和 L2（平方差）模式，可选忽略背景像素和计算 RGB 通道平均值。

## 结构体与接口

### 输入/输出

| 资源 | 类型 | 说明 |
|------|------|------|
| `gReference` | `Texture2D<float4>` | 参考图像 |
| `gSource` | `Texture2D<float4>` | 源图像 |
| `gWorldPosition` | `Texture2D<float4>` | 世界位置（用于背景检测） |
| `gResult` | `RWTexture2D<float4>` | 差异输出 |

### 常量缓冲区 `PerFrameCB`

| 成员 | 类型 | 说明 |
|------|------|------|
| `gResolution` | `uint2` | 分辨率 |
| `gIgnoreBackground` | `uint` | 是否忽略背景 |
| `gComputeDiffSqr` | `uint` | 是否计算平方差 |
| `gComputeAverage` | `uint` | 是否计算 RGB 平均 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `[numthreads(16,16,1)] void main(uint3)` | 计算着色器入口，计算逐像素差异 |

## 依赖关系 / import

无外部 import，仅使用内置类型。

## 实现细节

- 使用 `gWorldPosition.w != 0` 判断前景像素
- 差异计算：`diff = abs(source - reference)`
- L2 模式：`diff *= diff`
- 平均模式：`diff.rgb = (diff.r + diff.g + diff.b) / 3.0`
- 输出 alpha 通道固定为 0
