# EnvMapConverter.cs.slang 源码文档

> 路径: `Source/plugins/importers/PBRTImporter/EnvMapConverter.cs.slang`
> 类型: Slang 计算着色器
> 模块: plugins/importers/PBRTImporter

## 功能概述

GPU 计算着色器，将等面积八面体映射格式的环境贴图转换为经纬度映射格式。每个输出像素对应经纬度坐标，通过反向映射采样源八面体纹理。

## 结构体与接口

### `EnvMapConverter`

| 成员 | 类型 | 说明 |
|------|------|------|
| `src` | `Texture2D<float4>` | 源环境贴图（八面体映射） |
| `srcSampler` | `SamplerState` | 源纹理采样器 |
| `dst` | `RWTexture2D<float4>` | 输出环境贴图（经纬度映射） |
| `dstDim` | `uint2` | 输出纹理尺寸 |

#### 方法

| 方法签名 | 说明 |
|----------|------|
| `void execute(uint2 threadID)` | 将像素坐标转换为经纬度 UV，再转为方向向量，映射到八面体 UV 后采样源纹理 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void main(uint2 threadID)` | 入口点。线程组 [16,16,1]。调用 `gEnvMapConverter.execute()` |

## 依赖关系 / import

- `Utils.Math.MathHelpers` - 提供 `latlong_map_to_world()` 和 `ndir_to_oct_equal_area_unorm()` 函数
