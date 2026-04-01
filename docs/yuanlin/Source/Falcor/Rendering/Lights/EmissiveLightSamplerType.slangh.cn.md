# EmissiveLightSamplerType.slangh 源码文档

> 路径: `Source/Falcor/Rendering/Lights/EmissiveLightSamplerType.slangh`
> 类型: Slang 头文件（CPU/GPU 共享）
> 模块: Rendering/Lights

## 功能概述

本文件定义了自发光光源采样器类型的枚举，在 CPU 和 GPU 之间共享。同时定义了用于着色器条件编译的预处理器宏常量，并通过 `static_assert` 确保枚举值与宏常量保持一致。

## 结构体与接口

### `EmissiveLightSamplerType` (枚举)

| 值 | 数值 | 说明 |
|----|------|------|
| `Uniform` | 0 | 均匀采样 |
| `LightBVH` | 1 | BVH 引导采样 |
| `Power` | 2 | 按功率加权采样 |
| `Null` | 0xff | 空采样器（未启用） |

### 预处理器宏

| 宏名 | 值 | 说明 |
|------|-----|------|
| `EMISSIVE_LIGHT_SAMPLER_UNIFORM` | 0 | 用于着色器条件编译 |
| `EMISSIVE_LIGHT_SAMPLER_LIGHT_BVH` | 1 | 用于着色器条件编译 |
| `EMISSIVE_LIGHT_SAMPLER_POWER` | 2 | 用于着色器条件编译 |
| `EMISSIVE_LIGHT_SAMPLER_NULL` | 0xff | 用于着色器条件编译 |

## 实现细节

- 使用 `BEGIN_NAMESPACE_FALCOR` / `END_NAMESPACE_FALCOR` 支持 CPU/GPU 共享命名空间
- CPU 端通过 `FALCOR_ENUM_INFO` 和 `FALCOR_ENUM_REGISTER` 注册枚举元信息
- `static_assert` 在 HOST_CODE 下验证枚举值与宏常量一致

## 依赖关系

### import 引用

- `Utils/HostDeviceShared.slangh` — CPU/GPU 共享基础设施
