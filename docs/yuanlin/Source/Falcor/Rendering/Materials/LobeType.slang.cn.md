# LobeType.slang 源码文档

> 路径: `Source/Falcor/Rendering/Materials/LobeType.slang`
> 类型: Slang 着色器文件（主机/设备共享）
> 模块: Rendering/Materials

## 功能概述

定义 BSDF 叶瓣类型的标志位枚举，用于标识散射事件的类型（漫反射/镜面/Delta 反射/透射）。

## 结构体与接口

### `LobeType`（枚举）

| 标志 | 值 | 说明 |
|------|-----|------|
| `None` | 0x00 | 无叶瓣 |
| `DiffuseReflection` | 0x01 | 漫反射 |
| `SpecularReflection` | 0x02 | 镜面反射 |
| `DeltaReflection` | 0x04 | Delta 反射（理想镜面） |
| `DiffuseTransmission` | 0x10 | 漫透射 |
| `SpecularTransmission` | 0x20 | 镜面透射 |
| `DeltaTransmission` | 0x40 | Delta 透射（理想折射） |
| `Diffuse` | 0x11 | 所有漫反射+漫透射 |
| `Specular` | 0x22 | 所有镜面反射+镜面透射 |
| `Delta` | 0x44 | 所有 Delta 事件 |
| `NonDelta` | 0x33 | 所有非 Delta 事件 |
| `Reflection` | 0x0f | 所有反射 |
| `Transmission` | 0xf0 | 所有透射 |
| `All` | 0xff | 所有叶瓣 |

## 依赖关系

### include
- `Utils/HostDeviceShared.slangh`
