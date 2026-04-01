# BlitContext 源码文档

> 路径: `Source/Falcor/Core/API/BlitContext.h` / `BlitContext.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/API

## 功能概述

BlitContext 是一个辅助结构体，封装了 Blit（块传输）操作所需的所有上下文资源。Blit 是一种低级别的纹理复制操作，支持格式转换、缩放、采样模式选择以及复杂的通道变换。该结构体由 `RenderContext` 内部持有和使用。

## 类与接口

### `BlitContext`（结构体）
- **职责**: 持有 Blit 操作所需的全屏渲染通道、采样器、参数缓冲区等资源

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `pPass` | `ref<FullScreenPass>` | 全屏渲染通道，使用 `BlitReduction.3d.slang` 着色器 |
| `pFbo` | `ref<Fbo>` | 用于 Blit 输出的帧缓冲 |
| `pLinearSampler` | `ref<Sampler>` | 线性过滤采样器（标准缩减模式） |
| `pPointSampler` | `ref<Sampler>` | 点采样器（标准缩减模式） |
| `pLinearMinSampler` | `ref<Sampler>` | 线性过滤采样器（最小值缩减模式） |
| `pPointMinSampler` | `ref<Sampler>` | 点采样器（最小值缩减模式） |
| `pLinearMaxSampler` | `ref<Sampler>` | 线性过滤采样器（最大值缩减模式） |
| `pPointMaxSampler` | `ref<Sampler>` | 点采样器（最大值缩减模式） |
| `pBlitParamsBuffer` | `ref<ParameterBlock>` | 着色器参数块（偏移、缩放、通道变换矩阵） |
| `compTransVarOffset[4]` | `TypedShaderVarOffset[4]` | 四个颜色通道变换矩阵的偏移量 |

#### 构造函数
| 方法签名 | 说明 |
|----------|------|
| `BlitContext(Device* pDevice)` | 初始化全部资源：创建着色器、帧缓冲、六种采样器和参数缓冲区 |

## 实现细节

- 构造时创建 `FullScreenPass`，使用 `BlitReduction.3d.slang` 着色器
- 默认定义宏：`SAMPLE_COUNT=1`, `COMPLEX_BLIT=0`, `SRC_INT=0`, `DST_INT=0`
- 采样器覆盖三种缩减模式（Standard、Min、Max）和两种过滤模式（Linear、Point），共六个
- 通道变换矩阵默认为单位矩阵

## 依赖关系
### 本文件引用
- `Sampler.h`, `FBO.h`, `ParameterBlock.h`, `Device.h`
- `Core/Pass/FullScreenPass.h`, `Core/Program/Program.h`

### 被以下文件引用
- `RenderContext.h`/`RenderContext.cpp`（作为 Blit 操作的内部上下文）
