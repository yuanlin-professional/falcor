# NRDConstants.slang 源码文档

> 路径: `Source/Falcor/RenderPasses/Shared/Denoising/NRDConstants.slang`
> 类型: Slang/C++ 共享头文件
> 模块: 渲染通道/共享/降噪 (RenderPasses/Shared/Denoising)

## 功能概述

本文件定义了 NRD 降噪器使用的全局常量。这些常量通过 `HostDeviceShared.slangh` 实现 CPU/GPU 共享，确保主机端和着色器端使用一致的参数值。

## 常量定义

| 常量 | 类型 | 值 | 说明 |
|------|------|-----|------|
| `kNRDDepthRange` | `float` | `10000.0` | NRD 深度范围上限 |
| `kNRDInvalidPathLength` | `float` | `HLF_MAX` | 无效路径长度标记值（半精度浮点最大值） |
| `kNRDMinReflectance` | `float` | `0.01` | 最小反射率阈值，防止除零 |

## 依赖关系

### import

| 模块/文件 | 说明 |
|-----------|------|
| `Utils/HostDeviceShared.slangh` | CPU/GPU 共享宏 |
| `Utils/Math/MathConstants.slangh` | 数学常量（`HLF_MAX`） |

### 被以下文件引用

- `NRDHelpers.slang` — 使用 `kNRDMinReflectance` 等常量
