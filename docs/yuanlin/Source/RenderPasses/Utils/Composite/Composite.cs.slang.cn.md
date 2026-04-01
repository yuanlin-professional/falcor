# Composite.cs.slang 源码文档

> 路径: `Source/RenderPasses/Utils/Composite/Composite.cs.slang`
> 类型: 计算着色器
> 模块: RenderPasses/Utils/Composite

## 功能概述

此着色器实现了两缓冲区合成的 GPU 逻辑。根据编译时宏定义选择加法或乘法混合模式，并支持浮点、无符号整数和有符号整数三种输出格式。

## 结构体与接口

### 编译时宏定义

| 宏 | 说明 |
|----|------|
| `COMPOSITE_MODE` | 混合模式：`COMPOSITE_MODE_ADD`(0) 或 `COMPOSITE_MODE_MULTIPLY`(1) |
| `OUTPUT_FORMAT` | 输出格式：`OUTPUT_FORMAT_FLOAT`(0)、`OUTPUT_FORMAT_UINT`(1)、`OUTPUT_FORMAT_SINT`(2) |

### 常量缓冲区 `CB`

| 字段 | 类型 | 说明 |
|------|------|------|
| `frameDim` | `uint2` | 帧尺寸 |
| `scaleA` | `float` | 输入 A 缩放系数 |
| `scaleB` | `float` | 输入 B 缩放系数 |

### 资源绑定

| 名称 | 类型 | 说明 |
|------|------|------|
| `A` | `Texture2D<float4>` | 输入纹理 A |
| `B` | `Texture2D<float4>` | 输入纹理 B |
| `output` | `RWTexture2D<float4/uint4/int4>` | 输出纹理（类型由 `OUTPUT_FORMAT` 决定） |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void main(uint3 dispatchThreadId)` | 计算着色器入口，线程组 [16,16,1] |

## 依赖关系 / import

- `CompositeMode.slangh` — 提供混合模式和输出格式的宏定义

## 实现细节

当输出格式为非浮点时，使用 `round()` 进行四舍五入到最近偶数转换。
