# GaussianBlur.ps.slang 源码文档

> 路径: `Source/RenderPasses/Utils/GaussianBlur/GaussianBlur.ps.slang`
> 类型: 像素着色器
> 模块: RenderPasses/Utils/GaussianBlur

## 功能概述

此着色器实现了可分离高斯模糊的单方向模糊逻辑。通过编译时宏 `_HORIZONTAL_BLUR` 或 `_VERTICAL_BLUR` 选择模糊方向。支持普通 2D 纹理和 2D 纹理数组。

## 结构体与接口

### 编译时宏定义

| 宏 | 说明 |
|----|------|
| `_HORIZONTAL_BLUR` | 水平方向模糊 |
| `_VERTICAL_BLUR` | 垂直方向模糊 |
| `_KERNEL_WIDTH` | 高斯核宽度 |
| `_USE_TEX2D_ARRAY` | 使用 2D 纹理数组模式 |

### 结构体 `BlurPSIn`

| 字段 | 语义 | 说明 |
|------|------|------|
| `texC` | `TEXCOORD` | 纹理坐标 |
| `pos` | `SV_POSITION` | 屏幕位置 |
| `arrayIndex` | `SV_RenderTargetArrayIndex` | 纹理数组层索引（仅纹理数组模式） |

### 资源绑定

| 名称 | 类型 | 说明 |
|------|------|------|
| `gSrcTex` | `Texture2D` 或 `Texture2DArray` | 输入纹理 |
| `gSampler` | `SamplerState` | 线性 Clamp 采样器 |
| `weights` | `Buffer<float>` | 高斯核权重缓冲区 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float4 blur(float2 texC [, uint arrayIndex])` | 沿指定方向执行加权采样 |
| `float4 main(BlurPSIn pIn)` | 像素着色器入口 |

## 依赖关系 / import

- 无外部依赖

## 实现细节

1. **方向选择**: 通过编译时宏选择模糊方向 `dir = float2(1,0)` 或 `float2(0,1)`。
2. **加权采样**: 使用 `SampleLevel` 配合整数偏移进行采样，循环展开（`[ForceUnroll]`）以提高性能。
3. **纹理数组**: 当启用 `_USE_TEX2D_ARRAY` 时，使用 `float3(texC, arrayIndex)` 进行三维采样。
