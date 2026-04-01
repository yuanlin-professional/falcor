# ColorMapParams.slang 源码文档

> 路径: `Source/RenderPasses/DebugPasses/ColorMapPass/ColorMapParams.slang`
> 类型: Slang 共享头文件（主机/设备共享）
> 模块: RenderPasses/DebugPasses/ColorMapPass

## 功能概述

此文件定义了颜色映射渲染通道在 CPU 和 GPU 之间共享的枚举和结构体类型。使用 `HostDeviceShared.slangh` 宏实现跨主机/设备的类型兼容。

## 结构体与接口

### 枚举 `ColorMap`

| 值 | 说明 |
|----|------|
| `Grey` | 灰度映射 |
| `Jet` | Jet 彩虹映射 |
| `Viridis` | Viridis 感知均匀映射 |
| `Plasma` | Plasma 映射 |
| `Magma` | Magma 映射 |
| `Inferno` | Inferno 映射 |

使用 `FALCOR_ENUM_INFO` 和 `FALCOR_ENUM_REGISTER` 宏注册枚举信息，支持字符串<->枚举值的自动转换。

### 结构体 `ColorMapParams`

| 字段 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `minValue` | `float` | 0.0 | 值范围最小值 |
| `maxValue` | `float` | 1.0 | 值范围最大值 |

## 依赖关系 / import

- `Utils/HostDeviceShared.slangh` — 提供 `BEGIN_NAMESPACE_FALCOR`、`END_NAMESPACE_FALCOR` 等跨平台宏

## 实现细节

此文件使用 Falcor 的主机/设备共享机制，确保 C++ 端和 Slang 着色器端使用相同的类型定义和内存布局。
