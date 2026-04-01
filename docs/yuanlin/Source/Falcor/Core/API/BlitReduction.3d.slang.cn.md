# BlitReduction.3d.slang 源码文档

> 路径: `Source/Falcor/Core/API/BlitReduction.3d.slang`
> 类型: Slang 着色器文件
> 模块: Core/API

## 功能概述

BlitReduction.3d.slang 是 Blit（块传输）操作使用的着色器程序，实现了从源纹理到目标渲染目标的复制、缩放和格式转换。支持多种模式：普通采样、多重采样（MSAA）解析、整数格式转换以及复杂的通道重映射。

## 着色器资源

### 常量缓冲区 `BlitParamsCB`
| 变量 | 类型 | 说明 |
|------|------|------|
| `gOffset` | `float2` | 源纹理坐标偏移 |
| `gScale` | `float2` | 源纹理坐标缩放 |
| `gCompTransformR` | `float4` | R 通道线性组合系数 |
| `gCompTransformG` | `float4` | G 通道线性组合系数 |
| `gCompTransformB` | `float4` | B 通道线性组合系数 |
| `gCompTransformA` | `float4` | A 通道线性组合系数 |

### 纹理与采样器
| 资源 | 说明 |
|------|------|
| `gTex` | 源纹理（`Texture2D<float4>` 或 `Texture2DMS<float4>`） |
| `gSampler` | 普通模式采样器 |
| `gSamplerR/G/B/A` | 复杂 Blit 模式下各通道独立采样器 |

## 编译宏
| 宏 | 说明 |
|----|------|
| `SAMPLE_COUNT` | 多重采样数量，<=1 使用普通 Texture2D |
| `COMPLEX_BLIT` | 是否启用复杂 Blit（通道重映射） |
| `SRC_INT` | 源纹理是否为整数格式 |
| `DST_INT` | 目标是否为整数格式 |

## 着色器入口点

### `vsMain` (顶点着色器)
将输入顶点位置直接传递，纹理坐标应用 `gScale` 和 `gOffset` 变换。

### `psMain` (像素着色器)
根据编译宏选择不同的采样路径：
- **普通模式** (`SAMPLE_COUNT <= 1, !COMPLEX_BLIT`): 直接采样
- **复杂模式** (`COMPLEX_BLIT`): 各通道独立采样后通过点积应用变换矩阵
- **多重采样模式** (`SAMPLE_COUNT > 1`): 对所有样本求平均值

## 依赖关系
### 被以下文件引用
- `BlitContext.cpp`（创建使用此着色器的 FullScreenPass）
