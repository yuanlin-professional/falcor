# ConvertBufToTex.ps.slang 源码文档

> 路径: `Source/RenderPasses/OptixDenoiser/ConvertBufToTex.ps.slang`
> 类型: 像素着色器 (Pixel Shader)
> 模块: RenderPasses/OptixDenoiser

## 功能概述

该像素着色器将扁平缓冲区（Buffer）中的数据转换回纹理格式。用于 OptiX 降噪器输出后，将降噪结果从 CUDA/OptiX 使用的线性缓冲区转换回 DirectX 纹理以供后续渲染通道使用。

## 结构体与接口

### 常量缓冲区

| 成员 | 类型 | 说明 |
|------|------|------|
| `gStride` | `uint` | 缓冲区行步长（以像素为单位，通常等于图像宽度） |

### 资源绑定

| 名称 | 类型 | 说明 |
|------|------|------|
| `gInBuf` | `Buffer<float4>` | 输入的扁平缓冲区，存储降噪后的颜色数据 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float4 main(float2 texC: TEXCOORD, float4 posH: SV_POSITION) : SV_Target0` | 主入口，根据像素位置计算缓冲区索引并读取颜色值 |

## 依赖关系/import

无外部依赖。该着色器为独立的像素着色器，作为全屏通道（FullScreenPass）执行。

## 实现细节

使用 `SV_POSITION` 语义获取当前像素坐标，通过 `pixel.x + pixel.y * gStride` 计算扁平缓冲区中的线性索引，直接返回对应位置的 `float4` 颜色值作为渲染目标输出。
