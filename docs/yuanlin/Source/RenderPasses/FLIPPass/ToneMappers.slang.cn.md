# ToneMappers.slang 源码文档

> 路径: `Source/RenderPasses/FLIPPass/ToneMappers.slang`
> 类型: Slang 着色器
> 模块: RenderPasses/FLIPPass

## 功能概述

FLIP 算法使用的色调映射器定义，包含色调映射器类型枚举和色调映射函数。支持三种色调映射器：ACES、Hable 和 Reinhard，用于 HDR-FLIP 中将 HDR 输入转换为 LDR。

## 结构体与接口

### `FLIPToneMapperType` (枚举)

| 值 | 说明 |
|----|------|
| `ACES` (0) | ACES 近似色调映射 |
| `Hable` (1) | Hable（Uncharted 2）色调映射 |
| `Reinhard` (2) | Reinhard 色调映射 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float3 toneMap(float3 col, FLIPToneMapperType)` | 根据指定类型执行色调映射 |

## 依赖关系 / import

- `Utils/HostDeviceShared.slangh` — CPU/GPU 共享宏

## 实现细节

- ACES 使用 Knarkowicz 近似（含 0.6 预曝光取消因子）
- Hable 使用 Uncharted 2 参数（A=0.15, B=0.50 等），包含白点缩放
- Reinhard 使用亮度加权公式：`col / (Y + 1.0)`
- 所有映射器输出钳位到 [0, 1]
- 通用公式：`(k0*col^2 + k1*col + k2) / (k3*col^2 + k4*col + k5)`
- 仅在 GPU 端（非 `HOST_CODE`）定义 `toneMap` 函数
