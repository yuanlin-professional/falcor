# NRDData.slang 源码文档

> 路径: `Source/Falcor/RenderPasses/Shared/Denoising/NRDData.slang`
> 类型: Slang 着色器模块
> 模块: 渲染通道/共享/降噪 (RenderPasses/Shared/Denoising)

## 功能概述

本文件定义了 NRD 降噪器所需的核心数据类型，包括路径类型枚举 `NRDPathType` 和辐射度数据结构 `NRDRadiance`。`NRDRadiance` 存储每个样本的路径类型和辐射度值，用于 NRD 降噪器的分类降噪处理。

## 结构体与接口

### `NRDPathType` (枚举类)

| 值 | 说明 |
|-----|------|
| `Residual` (0) | 残余路径（未分配到以下任何类型） |
| `Diffuse` (1) | 漫反射路径（主要命中从漫反射 BSDF 叶采样） |
| `Specular` (2) | 镜面反射路径（主要命中从镜面 BSDF 叶采样） |
| `DeltaReflection` (3) | Delta 反射路径（主要命中为 Delta 反射） |
| `DeltaTransmission` (4) | Delta 透射路径（沿 Delta 透射事件传播直到首个非 Delta 事件） |

### `NRDRadiance`

- **职责**: 存储逐样本的路径类型和辐射度数据

#### 关键成员

| 成员 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `pathType` | `uint` | `Residual` | 路径类型 |
| `radiance` | `float3` | `0.0` | 辐射度值 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `NRDPathType getPathType()` | 获取路径类型 |
| `[mutating] void setPathType(NRDPathType)` | 设置路径类型 |
| `float3 getRadiance()` | 获取辐射度值 |
| `[mutating] void setRadiance(float3 color)` | 设置辐射度值 |

## 依赖关系

### 被以下文件引用

- `NRDBuffers.slang` — 使用 `NRDRadiance` 类型定义缓冲区
