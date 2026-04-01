# TAA.ps.slang 源码文档

> 路径: `Source/RenderPasses/TAA/TAA.ps.slang`
> 类型: 像素着色器 (Pixel Shader)
> 模块: RenderPasses/TAA

## 功能概述

该像素着色器实现了时序抗锯齿的核心算法。使用颜色空间变换（RGB -> YCgCo）、3x3 邻域颜色包围盒裁剪、Catmull-Rom 双三次历史采样和基于 Brian Karis 的抗闪烁技术。

## 结构体与接口

### 常量缓冲区

| 成员 | 类型 | 说明 |
|------|------|------|
| `gAlpha` | `float` | 当前帧混合权重（0 = 全部历史，1 = 全部当前帧） |
| `gColorBoxSigma` | `float` | 颜色包围盒的 sigma 系数（控制裁剪范围） |
| `gAntiFlicker` | `bool` | 是否启用抗闪烁 |

### 资源绑定

| 名称 | 类型 | 说明 |
|------|------|------|
| `gTexColor` | `Texture2D` | 当前帧颜色纹理 |
| `gTexMotionVec` | `Texture2D` | 运动向量纹理 |
| `gTexPrevColor` | `Texture2D` | 前帧颜色纹理 |
| `gSampler` | `SamplerState` | 线性过滤采样器 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float3 bicubicSampleCatmullRom(Texture2D, SamplerState, float2, float2)` | Catmull-Rom 双三次采样（9 次纹理读取优化为更少的 tap） |
| `float4 main(float2 texC: TEXCOORD) : SV_TARGET0` | 主入口，执行完整的 TAA 流程 |

## 依赖关系/import

| 模块 | 说明 |
|------|------|
| `Utils.Color.ColorHelpers` | 颜色空间转换函数（RGBToYCgCo、YCgCoToRGB） |

## 实现细节

1. **颜色空间变换**: 所有颜色操作在 YCgCo 空间进行（而非 RGB），因为 YCgCo 的亮度与色度分离特性更适合邻域裁剪操作。

2. **颜色包围盒**: 使用当前像素及其 8 邻域计算颜色均值和方差，构建 `[avg - sigma * std, avg + sigma * std]` 的包围盒。历史颜色被裁剪到此包围盒内以减少鬼影。

3. **运动向量选择**: 在 3x3 邻域中选择最长的运动向量用于历史重投影，以获得更鲁棒的结果。

4. **历史采样**: 使用 Catmull-Rom 双三次插值采样前帧颜色（来自 http://vec3.ca/bicubic-filtering-in-fewer-taps/ 的优化实现），提供比双线性插值更高质量的历史重建。

5. **抗闪烁**: 基于 Brian Karis 在 SIGGRAPH 2014 的技术（Epic Games），当历史颜色接近裁剪边界时降低混合权重，减少时序闪烁现象。公式为 `alpha = clamp(gAlpha * distToClamp / (distToClamp + boxSize), 0, 1)`。

6. **最终混合**: `lerp(history, color, alpha)`，其中 `alpha` 默认为 0.1（90% 历史权重）。结果从 YCgCo 转换回 RGB 空间输出。

7. **参考文献**:
   - GDC Vault: "From the Lab Bench: Real-Time Temporal Anti-Aliasing"
   - Cwyman: "Gaze-Tracked Foveated Rendering"
   - Brian Karis, SIGGRAPH 2014: "High-Quality Temporal Supersampling"
