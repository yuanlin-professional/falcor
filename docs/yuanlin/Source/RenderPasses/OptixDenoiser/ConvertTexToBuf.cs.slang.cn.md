# ConvertTexToBuf.cs.slang 源码文档

> 路径: `Source/RenderPasses/OptixDenoiser/ConvertTexToBuf.cs.slang`
> 类型: 计算着色器 (Compute Shader)
> 模块: RenderPasses/OptixDenoiser

## 功能概述

该计算着色器将 DirectX 纹理数据转换为 OptiX 降噪器所需的扁平缓冲区格式。是最基础的纹理到缓冲区转换着色器，直接逐像素复制 float4 数据。

## 结构体与接口

### 常量缓冲区

| 成员 | 类型 | 说明 |
|------|------|------|
| `gStride` | `uint` | 输出缓冲区行步长（以像素为单位，通常等于图像宽度） |

### 资源绑定

| 名称 | 类型 | 说明 |
|------|------|------|
| `gInTex` | `Texture2D<float4>` | 输入纹理（如含噪颜色图像或反照率） |
| `gOutBuf` | `RWBuffer<float4>` | 输出扁平缓冲区 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void main(uint3 thrdId: SV_DispatchThreadID)` | 主入口，线程组大小 [8, 8, 1]。根据线程 ID 计算缓冲区索引并复制纹理数据 |

## 依赖关系/import

无外部依赖。

## 实现细节

简单的逐像素复制操作：`gOutBuf[thrdId.x + thrdId.y * gStride] = gInTex[thrdId.xy]`。该着色器用于颜色输入和反照率输入的格式转换，不涉及任何数据变换。
