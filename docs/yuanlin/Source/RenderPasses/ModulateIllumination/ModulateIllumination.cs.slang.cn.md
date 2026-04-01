# ModulateIllumination.cs.slang 源码文档

> 路径: `Source/RenderPasses/ModulateIllumination/ModulateIllumination.cs.slang`
> 类型: 计算着色器 (Compute Shader)
> 模块: RenderPasses/ModulateIllumination

## 功能概述

光照调制的计算着色器，将多个分离的光照分量合成为最终输出颜色。合成公式为：

```
output = emission
       + diffuseReflectance * diffuseRadiance
       + specularReflectance * specularRadiance
       + deltaReflectionEmission
       + deltaReflectionReflectance * deltaReflectionRadiance
       + deltaTransmissionEmission
       + deltaTransmissionReflectance * deltaTransmissionRadiance
       + residualRadiance
```

每个分量通过编译时宏 `is_valid_*` 控制是否参与计算。

## 结构体与接口

### 输入纹理

| 纹理 | 说明 |
|------|------|
| `gEmission` | 自发光 |
| `gDiffuseReflectance` | 漫反射反照率 |
| `gDiffuseRadiance` | 漫反射辐照度 |
| `gSpecularReflectance` | 高光反照率 |
| `gSpecularRadiance` | 高光辐照度 |
| `gDeltaReflectionEmission` | Delta 反射自发光 |
| `gDeltaReflectionReflectance` | Delta 反射反照率 |
| `gDeltaReflectionRadiance` | Delta 反射辐照度 |
| `gDeltaTransmissionEmission` | Delta 透射自发光 |
| `gDeltaTransmissionReflectance` | Delta 透射反照率 |
| `gDeltaTransmissionRadiance` | Delta 透射辐照度 |
| `gResidualRadiance` | 残余辐照度 |

### 输出

| 纹理 | 说明 |
|------|------|
| `gOutput` | 合成后的最终颜色（RWTexture2D） |

### 常量缓冲区 `CB`

| 成员 | 类型 | 说明 |
|------|------|------|
| `frameDim` | `uint2` | 帧尺寸 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `[numthreads(16,16,1)] void main(uint3)` | 计算着色器入口 |

## 依赖关系 / import

无外部 import，仅使用编译时宏。

## 实现细节

- 使用 `#define is_valid(name) (is_valid_##name != 0)` 宏来检查输入可用性
- 反照率（Reflectance）与辐照度（Radiance）相乘实现调制
- 如果反照率不可用，仅使用辐照度
- 自发光和残余辐照度直接累加
- 输出 alpha 通道初始化为 0
