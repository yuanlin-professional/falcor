# SimplePostFX.cs.slang 源码文档

> 路径: `Source/RenderPasses/SimplePostFX/SimplePostFX.cs.slang`
> 类型: 计算着色器 (Compute Shader)
> 模块: RenderPasses/SimplePostFX

## 功能概述

此着色器实现了 SimplePostFX 渲染通道的三个 GPU 计算核心：下采样（downsample）、上采样（upsample）和后处理（runPostFX）。通过图像金字塔实现泛光效果，后处理阶段应用暗角、色差、桶形畸变、饱和度曲线和 ASC-CDL 色彩校正。

## 结构体与接口

### 常量缓冲区 `PerFrameCB`

| 字段 | 类型 | 说明 |
|------|------|------|
| `gResolution` | `uint2` | 当前处理分辨率 |
| `gInvRes` | `float2` | 分辨率的倒数 |
| `gBloomAmount` | `float` | 泛光混合比例 |
| `gVignetteAmount` | `float` | 暗角强度 |
| `gChromaticAberrationAmount` | `float` | 色差偏移量 |
| `gBarrelDistort` | `float2` | 桶形畸变参数（缩放因子, 畸变量） |
| `gSaturationCurve` | `float3` | 饱和度二次曲线系数 (A, B, C) |
| `gColorOffset` / `gColorScale` / `gColorPower` | `float3` | ASC-CDL 色彩校正参数 |
| `gStar` | `float` | 星光强度 |
| `gStarDir1/2/3` | `float2` | 三个星光方向向量（120 度间隔） |
| `gWipe` | `float` | 擦除位置（像素坐标） |
| `gInPlace` | `bool` | 是否原地更新金字塔 |

### 资源绑定

| 名称 | 类型 | 说明 |
|------|------|------|
| `gSrc` | `Texture2D<float4>` | 源纹理 |
| `gBloomed` | `Texture2D<float4>` | 泛光处理后的纹理 |
| `gDst` | `RWTexture2D<float4>` | 目标纹理（可读写） |
| `gLinearSampler` | `SamplerState` | 线性采样器 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float4 blurFilter2x2(Texture2D, float2, float, float)` | 2x2 双线性采样核，有效覆盖 4x4，使用二项式 1-3-3-1 权重 |
| `float4 blurFilter3x3(Texture2D, float2, float, float)` | 3x3 双线性采样核，有效覆盖 6x6，使用二项式 1-5-10-10-5-1 权重 |
| `float4 blurFilter5x5(Texture2D, float2, float, float)` | 5x5 双线性采样核，有效覆盖 10x10，用于下采样抗锯齿 |
| `void downsample(uint3)` | 计算着色器入口：使用 5x5 核进行下采样 |
| `void upsample(uint3)` | 计算着色器入口：使用 3x3 核进行上采样，可叠加星光效果 |
| `void runPostFX(uint3)` | 计算着色器入口：应用桶形畸变、色差、暗角、饱和度和色彩校正 |

## 依赖关系 / import

- `Utils/Color/ColorHelpers.slang` — 提供 `luminance()` 等颜色工具函数

## 实现细节

1. **下采样核**: 使用 5x5 双线性采样（25 个采样点），等效于 10x10 像素覆盖范围的二项式滤波器，确保高质量抗锯齿。
2. **上采样与泛光**: 从粗到细逐级混合，每级按 `gBloomAmount` 比例混合粗层到当前层。最终金字塔底层包含多尺度高斯叠加的泛光结果。
3. **星光**: 在上采样的特定级别，沿三个 120 度间隔方向（由 `gStarDir1/2/3` 定义）进行 41 次采样（-20 到 +20），使用 `1/(10+i*i)` 权重形成辐射状条纹。
4. **后处理管线**: 依次执行桶形畸变 -> 色差（RGB 三通道不同缩放采样） -> 暗角 -> 饱和度调整 -> ASC-CDL 色彩校正 -> 擦除对比。
5. **线程组**: 所有三个核心均使用 `[numthreads(16, 16, 1)]` 配置。
