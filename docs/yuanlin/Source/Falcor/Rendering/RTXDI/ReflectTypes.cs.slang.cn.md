# ReflectTypes.cs.slang 源码文档

> 路径: `Source/Falcor/Rendering/RTXDI/ReflectTypes.cs.slang`
> 类型: Slang 计算着色器
> 模块: Rendering/RTXDI

## 功能概述

本文件是一个辅助性的虚拟计算着色器，其唯一目的是提供结构化缓冲区类型的反射信息。Falcor 在创建结构化缓冲区时需要着色器反射数据来确定元素布局，但实际使用这些缓冲区的着色器在创建缓冲区时可能尚未编译。此虚拟程序通过声明 `PackedSurfaceData`、`PackedPolymorphicLight` 和 `RTXDI_PackedReservoir` 类型的结构化缓冲区，使 Falcor 能够提取这些类型的反射信息。

## 结构体与接口

本文件不定义新的结构体，仅声明以下缓冲区以触发反射：

| 缓冲区 | 类型 | 说明 |
|--------|------|------|
| `surfaceData` | `StructuredBuffer<PackedSurfaceData>` | 表面数据反射 |
| `lightInfo` | `StructuredBuffer<PackedPolymorphicLight>` | 光源信息反射 |
| `reservoirs` | `StructuredBuffer<RTXDI_PackedReservoir>` | 蓄水池数据反射 |

## 函数

| 函数 | 说明 |
|------|------|
| `main()` | 空入口点，不执行任何计算 |

## 依赖关系 / import

- `rtxdi/RtxdiParameters.h`（定义 `RTXDI_PackedReservoir` 类型）
- `PackedTypes`（本地模块）
