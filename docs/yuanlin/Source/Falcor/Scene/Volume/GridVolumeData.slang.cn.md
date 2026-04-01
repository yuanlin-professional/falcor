# GridVolumeData.slang 源码文档

> 路径: `Source/Falcor/Scene/Volume/GridVolumeData.slang`
> 类型: Slang 着色器文件（主机/设备共享）
> 模块: Scene/Volume

## 功能概述

定义网格体积的共享数据结构 `GridVolumeData`，在 CPU 和 GPU 之间共享。包含变换矩阵、包围盒、密度/发射参数、散射属性和温度设置。

## 结构体与接口

### `GridVolumeData`

| 成员 | 类型 | 说明 |
|------|------|------|
| `transform` | `float4x4` | 局部空间到世界空间的变换 |
| `invTransform` | `float4x4` | 世界空间到局部空间的逆变换 |
| `boundsMin` | `float3` | 世界空间包围盒最小点 |
| `densityScale` | `float` | 密度缩放因子，默认 1.0 |
| `boundsMax` | `float3` | 世界空间包围盒最大点 |
| `emissionScale` | `float` | 发射缩放因子，默认 1.0 |
| `densityGrid` | `uint` | 密度网格索引 |
| `emissionGrid` | `uint` | 发射网格索引 |
| `flags` | `uint` | 标志位（当前仅包含发射模式） |
| `anisotropy` | `float` | 相位函数各向异性参数 (g)，默认 0.0 |
| `albedo` | `float3` | 介质散射反照率，默认 (1,1,1) |
| `emissionTemperature` | `float` | 发射基础温度（K），默认 0.0 |

## 依赖关系 / import

- `Utils/HostDeviceShared.slangh` — 主机/设备共享宏
