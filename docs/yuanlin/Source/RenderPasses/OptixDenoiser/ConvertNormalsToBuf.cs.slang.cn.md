# ConvertNormalsToBuf.cs.slang 源码文档

> 路径: `Source/RenderPasses/OptixDenoiser/ConvertNormalsToBuf.cs.slang`
> 类型: 计算着色器 (Compute Shader)
> 模块: RenderPasses/OptixDenoiser

## 功能概述

该计算着色器将法线纹理从世界空间转换到视图空间，并将结果写入 OptiX 降噪器所需的扁平缓冲区格式。OptiX 降噪器要求引导法线位于视图空间（camera/eye space）。

## 结构体与接口

### 常量缓冲区

| 成员 | 类型 | 说明 |
|------|------|------|
| `gStride` | `uint` | 输出缓冲区行步长 |
| `gViewIT` | `float4x4` | 视图矩阵逆转置（用于将法线从世界空间变换到视图空间） |

### 资源绑定

| 名称 | 类型 | 说明 |
|------|------|------|
| `gInTex` | `Texture2D<float4>` | 输入法线纹理（[0,1] 编码） |
| `gOutBuf` | `RWBuffer<float3>` | 输出法线缓冲区（视图空间，float3 格式） |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void main(uint3 thrdId: SV_DispatchThreadID)` | 主入口，线程组大小 [8, 8, 1]。解码法线、变换到视图空间、归一化并写入缓冲区 |

## 依赖关系/import

无外部依赖。

## 实现细节

1. 法线从 [0, 1] 范围解码为 [-1, 1]: `normal = (texValue.xyz - 0.5) * 2.0`
2. 使用视图逆转置矩阵将法线变换到视图空间: `normal = mul(gViewIT, float4(normal, 0.0)).xyz`
3. 对有效法线（长度 > 0.01）进行归一化处理
4. 结果以 `float3` 格式写入扁平缓冲区，使用 `OPTIX_PIXEL_FORMAT_FLOAT3` 格式
