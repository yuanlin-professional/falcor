# ConvertMotionVectorInputs.cs.slang 源码文档

> 路径: `Source/RenderPasses/OptixDenoiser/ConvertMotionVectorInputs.cs.slang`
> 类型: 计算着色器 (Compute Shader)
> 模块: RenderPasses/OptixDenoiser

## 功能概述

该计算着色器将 Falcor 格式的运动向量（motion vector）转换为 OptiX 降噪器所需的格式。Falcor 的运动向量为归一化屏幕空间坐标（0~1 范围），而 OptiX 需要像素空间的运动向量且方向取反。

## 结构体与接口

### 常量缓冲区

| 成员 | 类型 | 说明 |
|------|------|------|
| `gStride` | `uint` | 输出缓冲区行步长（以像素为单位） |
| `gSize` | `uint2` | 图像尺寸（像素），用于将归一化坐标转换为像素坐标 |

### 资源绑定

| 名称 | 类型 | 说明 |
|------|------|------|
| `gInTex` | `Texture2D<float4>` | 输入的 Falcor 运动向量纹理 |
| `gOutBuf` | `RWBuffer<float2>` | 输出的 OptiX 运动向量缓冲区 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void main(uint3 thrdId: SV_DispatchThreadID)` | 主入口，线程组大小 [8, 8, 1]。读取运动向量，乘以图像尺寸并取反方向 |

## 依赖关系/import

无外部依赖。

## 实现细节

转换公式: `optixMVec = gInTex[pixel].xy * gSize`，然后取反 `float2(-optixMVec.x, -optixMVec.y)`。方向取反是因为 Falcor 和 OptiX 对运动向量的方向约定不同。
