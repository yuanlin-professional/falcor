# flip.hlsli 源码文档

> 路径: `Source/RenderPasses/FLIPPass/flip.hlsli`
> 类型: HLSL 头文件
> 模块: RenderPasses/FLIPPass

## 功能概述

FLIP 算法使用的颜色映射查找表数据文件。包含两个 256 元素的 `float3` 静态数组：`MagmaMap`（用于 FLIP 误差可视化）和 `ViridisMap`（用于 HDR-FLIP 曝光图可视化）。

## 结构体与接口

### 静态常量数组

| 数组 | 大小 | 说明 |
|------|------|------|
| `MagmaMap[256]` | `float3` | Magma 颜色映射表，用于将 FLIP 误差值（0-1）映射为热力图颜色 |
| `ViridisMap[256]` | `float3` | Viridis 颜色映射表，用于将曝光级别映射为可视化颜色 |

## 依赖关系 / import

无外部依赖。

## 实现细节

- 数据来源于 NVIDIA FLIP 项目（https://github.com/NVlabs/flip）
- 基于以下论文：
  - "FLIP: A Difference Evaluator for Alternating Images" (HPG 2020)
  - "Visualizing Errors in Rendered High Dynamic Range Images" (Eurographics 2021)
  - "Visualizing and Communicating Errors in Rendered Images" (Ray Tracing Gems II, 2021)
- 通过索引访问：`MagmaMap[int(value * 255.0 + 0.5)]`
