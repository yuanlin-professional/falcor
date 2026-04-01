# PixelStatsShared.slang 源码文档

> 路径: `Source/Falcor/Rendering/Utils/PixelStatsShared.slang`
> 类型: Slang 着色器模块（主机/设备共享）
> 模块: Rendering/Utils

## 功能概述

本文件定义了像素统计系统中主机端（C++）和设备端（Slang 着色器）共享的枚举类型。通过 `HostDeviceShared.slangh` 的 `BEGIN_NAMESPACE_FALCOR` / `END_NAMESPACE_FALCOR` 宏实现双端兼容。

## 结构体与接口

### 枚举 `PixelStatsRayType`

| 值 | 数值 | 说明 |
|----|------|------|
| `Visibility` | 0 | 可见性光线（阴影射线） |
| `ClosestHit` | 1 | 最近命中光线（路径延续射线） |
| `Count` | — | 光线类型总数（用于数组大小） |

## 依赖关系 / import

- `Utils/HostDeviceShared.slangh`（主机/设备共享宏）

## 实现细节

- 使用 `#pragma once` 防止重复包含
- 枚举值在 C++ 端用于索引统计数组，在 Slang 端用于选择光线计数纹理
- `Count` 作为哨兵值，同时定义统计缓冲区的数组大小
