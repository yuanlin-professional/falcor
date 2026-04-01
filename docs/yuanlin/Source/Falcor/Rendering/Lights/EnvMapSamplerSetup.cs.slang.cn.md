# EnvMapSamplerSetup.cs.slang 源码文档

> 路径: `Source/Falcor/Rendering/Lights/EnvMapSamplerSetup.cs.slang`
> 类型: Slang 计算着色器
> 模块: Rendering/Lights

## 功能概述

本计算着色器负责从环境贴图构建分层重要性贴图的基层（最精细 mip 级别）。对于输出贴图的每个纹素，着色器在对应的等面积八面体映射区域内进行多次子采样，将环境贴图的 RGB 辐射度转换为亮度值并取平均，写入重要性贴图。后续的 mipmap 层级由硬件 `generateMips()` 自动生成。生成的重要性贴图被 `EnvMapSampler.slang` 用于环境贴图的重要性采样。

## 结构体与接口

### 常量缓冲区 `CB`

| 成员 | 类型 | 说明 |
|------|------|------|
| `outputDim` | `uint2` | 重要性贴图的纹素分辨率 |
| `outputDimInSamples` | `uint2` | 重要性贴图的采样分辨率（纹素分辨率 * 每纹素采样数） |
| `numSamples` | `uint2` | 每纹素的子采样数 (sx, sy) |
| `invSamples` | `float` | 1 / (sx * sy)，用于平均化 |

### 资源绑定

| 名称 | 类型 | 说明 |
|------|------|------|
| `gEnvSampler` | `SamplerState` | 环境贴图采样器 |
| `gEnvMap` | `Texture2D<float4>` | 环境贴图纹理 |
| `gImportanceMap` | `RWTexture2D<float>` | 输出重要性贴图 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `[numthreads(16, 16, 1)] void main(uint3 dispatchThreadID : SV_DispatchThreadID)` | 主计算入口，计算每个输出纹素的平均亮度 |

## 实现细节

1. 对每个输出纹素执行 `numSamples.x * numSamples.y` 次子采样
2. 每个子采样计算在等面积八面体映射中的位置 `p`
3. 将八面体坐标转换为方向向量（`oct_to_ndir_equal_area_unorm`）
4. 再将方向转换为经纬度贴图 UV（`world_to_latlong_map`）
5. 从环境贴图采样 RGB 辐射度，转换为亮度（`luminance`）
6. 累加所有子采样的亮度并取平均，写入重要性贴图

## 依赖关系

### import 引用

- `Utils.Math.MathHelpers` — 八面体映射、经纬度映射等数学函数
- `Utils.Color.ColorHelpers` — 亮度计算函数（`luminance`）
