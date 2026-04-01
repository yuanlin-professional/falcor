# PixelStats.cs.slang 源码文档

> 路径: `Source/Falcor/Rendering/Utils/PixelStats.cs.slang`
> 类型: Slang 计算着色器
> 模块: Rendering/Utils

## 功能概述

本文件实现了逐像素总光线计数的计算着色器。它将各类型光线计数纹理（可见性光线、最近命中光线等）求和，输出到单一的总计数纹理中。该着色器由 `PixelStats::computeRayCountTexture()` 按需调度执行。

## 结构体与接口

### 常量缓冲区 `CB`

| 成员 | 类型 | 说明 |
|------|------|------|
| `gFrameDim` | `uint2` | 帧尺寸 |

### 资源

| 资源 | 类型 | 说明 |
|------|------|------|
| `gStatsRayCount[]` | `Texture2D<uint>[Count]` | 各类型光线计数输入纹理 |
| `gStatsRayCountTotal` | `RWTexture2D<uint>` | 总光线计数输出纹理 |

## 函数

| 函数 | 说明 |
|------|------|
| `main(uint3 dispatchThreadId)` | 计算着色器入口点，[numthreads(16, 16, 1)]，遍历所有光线类型求和 |

## 依赖关系 / import

- `PixelStatsShared`（`PixelStatsRayType::Count` 枚举值）

## 实现细节

- 对超出帧尺寸的线程直接返回
- 循环遍历 `PixelStatsRayType::Count` 个纹理，逐像素累加所有光线类型计数
