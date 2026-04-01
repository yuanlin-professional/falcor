# RenderPassStandardFlags 源码文档

> 路径: `Source/Falcor/RenderGraph/RenderPassStandardFlags.h`
> 类型: C++ 头文件（仅声明）
> 模块: RenderGraph (渲染图)

## 功能概述

定义了渲染通道之间通过全局字典传递的标准标志和键名常量。这些标志用于通知渲染通道自上一帧以来的状态变化，以及传递通用的渲染参数。

## 类与接口

### `RenderPassRefreshFlags` 枚举

- **类型**: `enum class : uint32_t`
- **职责**: 标识帧间状态变化类型，支持按位或操作。

| 枚举值 | 值 | 说明 |
|--------|-----|------|
| `None` | `0x0` | 无变化 |
| `LightingChanged` | `0x1` | 光照发生变化 |
| `RenderOptionsChanged` | `0x2` | 影响渲染的选项发生变化 |

### 标准字典键名常量

| 常量 | 值 | 说明 |
|------|----|------|
| `kRenderPassRefreshFlags` | `"_refreshFlags"` | 刷新标志，通过字典传递给 `RenderPass::execute()` |
| `kRenderPassPRNGDimension` | `"_prngDimension"` | 首个可用的伪随机数生成器维度 |
| `kRenderPassGBufferAdjustShadingNormals` | `"_gbufferAdjustShadingNormals"` | 是否调整主命中的着色法线 |

## 依赖关系

### 本文件引用

- `Core/Macros.h` - FALCOR_ENUM_CLASS_OPERATORS 宏

### 被以下文件引用

- 各种渲染通道实现（通过字典读取这些标志来调整行为）

## 实现细节

- **字典传递**: 这些标志和参数通过 `RenderData::getDictionary()` 返回的字典在渲染通道之间传递，使用字符串键进行查找。
- **位操作**: `RenderPassRefreshFlags` 通过 `FALCOR_ENUM_CLASS_OPERATORS` 宏启用位操作，支持多个标志的组合。
- **命名约定**: 所有标准键名以下划线 `_` 开头，以避免与用户自定义键冲突。
