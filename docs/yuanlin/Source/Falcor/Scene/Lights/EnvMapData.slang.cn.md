# EnvMapData.slang 源码文档

> 路径: `Source/Falcor/Scene/Lights/EnvMapData.slang`
> 类型: Slang 着色器文件（主机/设备共享）
> 模块: Scene/Lights

## 功能概述

定义环境贴图的共享数据结构 `EnvMapData`，在 CPU 和 GPU 之间共享使用。包含变换矩阵、色调和强度参数。

## 结构体与接口

### `EnvMapData`

| 成员 | 类型 | 说明 |
|------|------|------|
| `transform` | `float3x4` | 局部空间到世界空间的变换矩阵 |
| `invTransform` | `float3x4` | 世界空间到局部空间的逆变换矩阵 |
| `tint` | `float3` | 色调（RGB 乘法因子），默认 (1,1,1) |
| `intensity` | `float` | 辐照度缩放因子，默认 1.0 |

## 依赖关系 / import

- `Utils/HostDeviceShared.slangh` — 主机/设备共享宏定义
