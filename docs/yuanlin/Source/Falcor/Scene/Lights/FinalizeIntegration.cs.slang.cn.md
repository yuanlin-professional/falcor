# FinalizeIntegration.cs.slang 源码文档

> 路径: `Source/Falcor/Scene/Lights/FinalizeIntegration.cs.slang`
> 类型: Slang 计算着色器
> 模块: Scene/Lights

## 功能概述

计算着色器，用于计算每个自发光三角形的最终平均辐照度和光通量。读取纹理积分阶段产生的纹素累加数据和最大值数据，结合材质自发光属性，输出每个三角形的平均辐照度和通量值。

## 结构体与接口

### 资源绑定

| 资源 | 类型 | 说明 |
|------|------|------|
| `gTriangleCount` | `uint` (cbuffer) | 三角形总数 |
| `gPointSampler` | `SamplerState` | 最近邻采样器 |
| `gTexelMax` | `ByteAddressBuffer` | 最大纹素值 |
| `gTexelSum` | `ByteAddressBuffer` | 纹素累加值（64 位定点） |
| `gTriangleData` | `StructuredBuffer<PackedEmissiveTriangle>` | 三角形几何数据 |
| `gFluxData` | `RWStructuredBuffer<EmissiveFlux>` | 输出通量数据 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `uint64_t asuint64(uint low, uint high)` | 将两个 32 位值组合为 64 位 |
| `void finalizeIntegration(uint3 DTid)` | 主入口：计算每个三角形的平均辐照度和通量 |

## 依赖关系 / import

- `Utils/Math/MathConstants.slangh` — 数学常量（M_PI）
- `Scene.Scene` — 场景材质数据访问
- `Utils.Color.ColorHelpers` — 亮度计算（luminance）

## 实现细节

- 线程组大小：256x1x1，调度为 Y 方向的多行。
- 对于纹理化的自发光：从定点格式的累加数据中恢复浮点值，除以覆盖权重得到平均自发光颜色。
- 对于纹理空间退化的三角形（覆盖权重为零）：采样三个顶点处的自发光颜色取平均。
- 对于非纹理化的自发光：直接使用材质的 emissive 颜色。
- 通量计算：`luminance(averageRadiance) * area * PI`（假设漫反射发射器，单侧半球积分）。
