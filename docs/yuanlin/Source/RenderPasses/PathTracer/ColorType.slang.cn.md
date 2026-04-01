# ColorType.slang 源码文档

> 路径: `Source/RenderPasses/PathTracer/ColorType.slang`
> 类型: Slang 着色器
> 模块: RenderPasses/PathTracer

## 功能概述

定义了路径追踪器使用的每样本颜色存储类型 `ColorType`。根据编译时宏 `COLOR_FORMAT` 的不同，支持 RGBA32F 浮点格式和 LogLuvHDR 压缩格式两种颜色编码方式，用于在紧凑的每样本缓冲区中高效存储颜色数据。

## 结构体与接口

### `ColorType`

| 成员/方法 | 说明 |
|-----------|------|
| `data` | 底层存储数据（RGBA32F 模式为 `float4`，LogLuvHDR 模式为 `uint`） |
| `get()` → `float3` | 解码并返回 RGB 颜色值 |
| `set(float3 color)` | 编码并存储 RGB 颜色值 |

## 函数

- `get()`: 根据当前格式解码颜色。RGBA32F 直接返回 `.rgb`；LogLuvHDR 调用 `decodeLogLuvHDR`
- `set(float3)`: 根据当前格式编码颜色。RGBA32F 存储为 `float4(color, 1)`；LogLuvHDR 调用 `encodeLogLuvHDR`

## 依赖关系 / import

- `Params` — 获取 `COLOR_FORMAT` 宏定义
- `Utils.Math.PackedFormats` — LogLuvHDR 编解码函数
