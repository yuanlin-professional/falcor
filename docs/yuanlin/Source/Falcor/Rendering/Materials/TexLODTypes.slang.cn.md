# TexLODTypes.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/TexLODTypes.slang`
> 类型: Slang 着色器文件（主机/设备共享）
> 模块: Rendering/Materials

## 功能概述

定义纹理 LOD 系统的枚举类型，在 CPU 和 GPU 之间共享。

## 结构体与接口

### `TexLODMode`（枚举）

| 值 | 说明 |
|----|------|
| `Mip0` | 始终使用 Mip0 |
| `RayCones` | 光线锥方法 |
| `RayDiffs` | 光线微分方法 |
| `Stochastic` | 随机方法 |

### `RayConeMode`（枚举）

| 值 | 说明 |
|----|------|
| `Combo` | 首次命中用光线微分，后续用平均边曲率 |
| `Unified` | 首次命中用插值边曲率，后续用平均边曲率 |

## 依赖关系

### include
- `Utils/HostDeviceShared.slangh`
