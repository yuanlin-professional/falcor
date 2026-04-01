# EmissiveLightSamplerHelpers.slang 源码文档

> 路径: `Source/Falcor/Rendering/Lights/EmissiveLightSamplerHelpers.slang`
> 类型: Slang 着色器文件
> 模块: Rendering/Lights

## 功能概述

本着色器文件提供了自发光光源采样器实现共用的工具函数。这些函数依赖于 `LightCollection`（全局发光三角形列表容器），主要包含对发光三角形的均匀面积采样以及给定命中点的概率密度函数（PDF）评估。所有采样器实现（Uniform、Power、LightBVH）均使用这些辅助函数。

## 函数

| 函数签名 | 说明 |
|----------|------|
| `bool sampleTriangle(const float3 posW, const uint triangleIndex, const float2 u, out TriangleLightSample ls)` | 在指定发光三角形上按面积均匀采样一点，计算辐射方向、距离、发射辐射度和相对于立体角的 PDF |
| `float evalTrianglePdf(const float3 posW, const TriangleLightHit hit)` | 给定着色点和发光三角形上的命中点，计算相对于立体角的 PDF |

## 实现细节

### `sampleTriangle`

1. 使用 `sample_triangle(u)` 生成三角形上的均匀重心坐标
2. 计算采样点世界坐标、光线方向和距离
3. 检查三角形是否背面朝向（`cosTheta <= 0` 则拒绝）
4. 通过材质系统评估发射辐射度（`evalEmissive`）
5. 如果材质有光照剖面（Light Profile），额外乘以剖面因子
6. 计算立体角 PDF：`distSqr / (cosTheta * area)`

### `evalTrianglePdf`

1. 计算着色点到命中点的方向和距离
2. 计算命中三角形法线与光线方向的余弦
3. 返回立体角 PDF：`distSqr / (cosTheta * area)`

## 依赖关系

### import 引用

- `Utils/Math/MathConstants.slangh` — 数学常量（FLT_MIN 等）
- `Scene.Scene` — 场景全局变量 `gScene`
- `Scene.Lights.LightCollection` — 光源集合（导出）
- `Utils.Math.MathHelpers` — 数学辅助函数
- `Rendering.Lights.EmissiveLightSamplerInterface` — 采样数据结构定义
