# ReflectTypes.cs.slang 源码文档

> 路径: `Source/RenderPasses/PathTracer/ReflectTypes.cs.slang`
> 类型: Slang 计算着色器
> 模块: RenderPasses/PathTracer

## 功能概述

一个空操作的辅助计算着色器，仅用于提供结构化缓冲区类型的反射数据。Falcor 需要反射信息来创建结构化缓冲区，但实际的着色器程序在创建缓冲区时可能尚未就绪，因此使用此辅助程序来简化流程。

## 结构体与接口

声明了以下结构化缓冲区，用于类型反射：

| 缓冲区 | 类型 | 说明 |
|--------|------|------|
| `sampleColor` | `StructuredBuffer<ColorType>` | 每样本颜色 |
| `sampleGuideData` | `StructuredBuffer<GuideData>` | 每样本引导数据 |
| `sampleNRDRadiance` | `StructuredBuffer<NRDRadiance>` | NRD 辐射度 |
| `sampleNRDHitDist` | `StructuredBuffer<float>` | NRD 命中距离 |
| `sampleNRDPrimaryHitNeeOnDelta` | `StructuredBuffer<float4>` | NRD Delta 主命中 NEE |
| `sampleNRDEmission` | `StructuredBuffer<float4>` | NRD 发射 |
| `sampleNRDReflectance` | `StructuredBuffer<float4>` | NRD 反射率 |

## 依赖关系 / import

- `RenderPasses.Shared.Denoising.NRDData` — NRD 数据类型
- `PathTracer`, `ColorType`, `GuideData` — 本模块核心类型
