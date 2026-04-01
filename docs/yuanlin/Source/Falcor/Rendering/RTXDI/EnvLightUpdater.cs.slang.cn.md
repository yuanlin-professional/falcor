# EnvLightUpdater.cs.slang 源码文档

> 路径: `Source/Falcor/Rendering/RTXDI/EnvLightUpdater.cs.slang`
> 类型: Slang 计算着色器
> 模块: Rendering/RTXDI

## 功能概述

本文件实现了环境光的亮度和 PDF 纹理更新计算着色器。它对场景的环境贴图进行逐像素采样，计算亮度值和加权 PDF（考虑极区压缩的相对立体角校正），将结果写入对应的纹理资源供 RTXDI 预采样使用。

## 结构体与接口

### `EnvLightUpdater`

| 成员 | 类型 | 说明 |
|------|------|------|
| `envLightLuminance` | `RWTexture2D<float>` | 环境光亮度输出纹理 |
| `envLightPdf` | `RWTexture2D<float>` | 环境光 PDF 输出纹理 |
| `texDim` | `uint2` | 纹理尺寸 |

## 函数

| 函数 | 说明 |
|------|------|
| `execute(uint2 texelID)` | 计算指定纹素的环境光亮度和 PDF |
| `main(uint2 threadID)` | 计算着色器入口点，[numthreads(16, 16, 1)] |

## 依赖关系 / import

- `Utils/Math/MathConstants.slangh`
- `Scene.Scene`（访问 `gScene.envMap`）
- `Utils.Color.ColorHelpers`（`luminance()` 函数）

## 实现细节

- UV 坐标计算使用半像素偏移保证中心采样
- PDF 计算公式: `PDF = cos(elevation) * luminance`，其中 `elevation` 是纬度角
- `cos(elevation)` 校正了等距柱状投影在极区的立体角压缩，使采样概率与实际辐射通量成正比
- 全局参数块 `gEnvLightUpdater` 通过 `ParameterBlock<EnvLightUpdater>` 绑定
