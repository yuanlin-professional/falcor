# SampleTexture.slang 源码文档

> 路径: `Source/Modules/USDUtils/PreviewSurfaceConverter/SampleTexture.slang`
> 类型: Slang 着色器模块
> 模块: USDUtils / PreviewSurfaceConverter

## 功能概述

提供纹理采样辅助函数，用于在不同分辨率的源纹理和输出纹理之间正确采样。通过计算正确的 mip 梯度来确保采样质量。

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float4 sampleTexture(Texture2D<float4> tex, SamplerState sampler, int2 pos, int2 outDim)` | 在给定输出像素位置采样源纹理。自动计算源/目标分辨率比值作为 mip 梯度参数 |

## 实现细节

- 采样位置计算：`(pos + 0.5) / outDim` 转换为归一化 UV 坐标。
- 使用 `SampleGrad` 而非 `SampleLevel`，根据源/目标像素比例计算正确的 mip 偏导数。

## 依赖关系 / import

- 无外部依赖

### 被以下文件引用

- `CreateSpecularTexture.cs.slang`
- `PackBaseColorAlpha.cs.slang`
