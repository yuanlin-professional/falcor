# FLIPPass.cs.slang 源码文档

> 路径: `Source/RenderPasses/FLIPPass/FLIPPass.cs.slang`
> 类型: 计算着色器 (Compute Shader)
> 模块: RenderPasses/FLIPPass

## 功能概述

FLIP 图像质量度量的核心计算着色器。实现了完整的 LDR-FLIP 和 HDR-FLIP 算法，包括颜色空间转换（sRGB -> YCxCz -> CIELab）、对比敏感度函数（CSF）空间滤波、特征检测（边缘和点梯度）、Hunt 调整、HyAB 颜色差异度量以及误差重分布。

## 结构体与接口

### 输入/输出

| 资源 | 类型 | 说明 |
|------|------|------|
| `gTestImage` | `Texture2D` | 测试图像 |
| `gReferenceImage` | `Texture2D` | 参考图像 |
| `gFLIPErrorMap` | `RWTexture2D<float4>` | FLIP 误差图（高精度，用于计算） |
| `gFLIPErrorMapDisplay` | `RWTexture2D<float4>` | FLIP 误差图（用于显示） |
| `gExposureMapDisplay` | `RWTexture2D<float4>` | HDR-FLIP 曝光图 |

### 常量缓冲区 `PerFrameCB`

包含分辨率、HDR 标记、Magma 标记、钳位标记、曝光参数和监视器参数。

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float ppd()` | 计算每度像素数（PPD） |
| `float3 getPixel(Texture2D, int2, float)` | 获取像素颜色（含曝光补偿和色调映射），转换到 YCxCz |
| `float calculateWeight(float, float4)` | 计算 CSF 滤波权重 |
| `float HyAB(float3, float3)` | 计算 HyAB 颜色差异 |
| `float3 Hunt(float3)` | Hunt 色度调整 |
| `float redistributeErrors(float, float)` | 误差重分布（结合颜色和特征差异） |
| `float LDRFLIP(uint2, float)` | LDR-FLIP 核心算法 |
| `float HDRFLIP(uint2)` | HDR-FLIP（多曝光取最大值） |
| `float FLIP(uint2)` | FLIP 入口（根据 HDR 标记调度） |
| `[numthreads(32,32,1)] void main(uint3)` | 计算着色器入口 |

## 依赖关系 / import

- `Utils/Math/MathConstants.slangh` — 数学常量
- `flip.hlsli` — Magma 和 Viridis 颜色映射表
- `ToneMappers` — 色调映射函数
- `Utils.Color.ColorHelpers` — 颜色空间转换

## 实现细节

- CSF 滤波使用三对不同参数（A/RG/BY 通道）的高斯函数
- 特征检测使用高斯二阶导（点检测）和高斯一阶导（边缘检测）
- 滤波半径由 PPD 和最大空间频率决定
- 误差重分布使用感知截断和特征调制
- HDR-FLIP 在多个曝光级别上运行 LDR-FLIP，记录最大误差对应的曝光索引
