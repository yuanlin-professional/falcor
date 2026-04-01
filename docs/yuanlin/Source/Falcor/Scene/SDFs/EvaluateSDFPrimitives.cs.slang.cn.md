# EvaluateSDFPrimitives.cs.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/EvaluateSDFPrimitives.cs.slang`
> 类型: 计算着色器 (.cs.slang)
> 模块: Scene/SDFs

## 功能概述

计算着色器,用于在 GPU 上评估 SDF 基元并将结果写入网格值。遍历所有基元,在每个网格角点位置计算有符号距离值。支持输出到 3D 纹理或线性缓冲区,以及与已有距离场合并。

## 结构体与接口

### 常量缓冲区

| 变量 | 类型 | 说明 |
|------|------|------|
| `gGridWidth` | `uint` | 网格宽度(以体素为单位) |
| `gPrimitiveCount` | `uint` | 基元数量 |

### 资源绑定

| 资源 | 类型 | 说明 |
|------|------|------|
| `gPrimitives` | `StructuredBuffer<SDF3DPrimitive>` | 基元数据缓冲区 |
| `gOldValues` | `Texture3D<float>` | 已有距离场(用于合并模式) |
| `gValues` | `RWTexture3D<float>` 或 `RWBuffer<float>` | 输出距离值 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void writeDistanceToValues(uint3 valueCoords, float sd)` | 将距离值写入输出(纹理或缓冲区) |
| `void main(uint3 dispatchThreadID, uint3 groupThreadID)` | 主入口,每个线程处理一个网格角点 |

## 条件编译宏

| 宏 | 说明 |
|----|------|
| `_USE_SD_FIELD_3D_TEXTURE` | 定义时输出到 3D 纹理,否则输出到线性缓冲区 |
| `_MERGE_WITH_THE_SD_FIELD` | 定义时将新基元结果与已有距离场合并 |

## 依赖关系

### import
- `Scene.SDFs.SDF3DPrimitive`
- `Utils.Math.PackedFormats`

### include
- `Utils/Math/MathConstants.slangh`

## 实现细节

- 线程组大小为 4x4x4
- 每个线程计算一个网格角点的有符号距离值
- 网格局部空间为 `[-0.5, 0.5]`,角点位置由 `p = -0.5 + float3(valueCoords) / gGridWidth` 计算
- 合并模式下,先加载已有距离值作为初始值,再逐基元评估和应用 CSG 操作
