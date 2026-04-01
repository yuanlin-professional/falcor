# PackBaseColorAlpha.cs.slang 源码文档

> 路径: `Source/Modules/USDUtils/PreviewSurfaceConverter/PackBaseColorAlpha.cs.slang`
> 类型: Slang 计算着色器
> 模块: USDUtils / PreviewSurfaceConverter

## 功能概述

GPU 计算着色器，将基础颜色（RGB）和不透明度（Alpha）合并为单张 RGBA 纹理。两者可以分别为纹理或常量值，且可能具有不同的分辨率。

## 结构体与接口

### 常量缓冲区 `CB`

| 成员 | 类型 | 说明 |
|------|------|------|
| `opacityChannel` | `int` | 不透明度纹理通道索引（<0 表示使用常量值） |
| `opacityValue` | `float` | 不透明度常量值 |
| `baseColorTextureValid` | `int` | 基础颜色纹理是否有效（0 或 1） |
| `baseColorValue` | `float4` | 基础颜色常量值 |
| `outDim` | `uint2` | 输出纹理尺寸 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void packBaseColorAlpha(uint3 threadId)` | 入口点。线程组 [16,16,1]。分别采样或使用常量的基础颜色和不透明度，输出 `float4(baseColor.rgb, opacity)` |

## 依赖关系 / import

- `SampleTexture` - 提供 `sampleTexture()` 辅助函数
