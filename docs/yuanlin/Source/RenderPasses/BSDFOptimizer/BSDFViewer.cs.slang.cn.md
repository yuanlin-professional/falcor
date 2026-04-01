# BSDFViewer.cs.slang 源码文档（BSDFOptimizer 目录）

> 路径: `Source/RenderPasses/BSDFOptimizer/BSDFViewer.cs.slang`
> 类型: 计算着色器 (Slang)
> 模块: RenderPasses/BSDFOptimizer

## 功能概述

BSDFOptimizer 模块的可视化着色器。显示三视口比较视图：左侧为初始（当前优化中的）材质的双向散射分布函数切片，中间为两者的绝对差异，右侧为参考材质的双向散射分布函数切片。与 BSDFViewer 目录下的同名着色器不同，此文件专为优化器的三视口对比设计。

## 结构体与接口

### `BSDFViewer`（ParameterBlock）

| 字段 | 类型 | 说明 |
|------|------|------|
| `params` | `BSDFOptimizerParams` | 优化器参数（非 BSDFViewerParams） |
| `output` | `RWTexture2D<float4>` | 输出纹理 |

### `SurfaceData`（内部结构）

| 字段 | 类型 | 说明 |
|------|------|------|
| `sd` | `ShadingData` | 着色数据 |
| `bsdfProperties` | `BSDFProperties` | 双向散射分布函数属性 |
| `wo` | `float3` | 出射方向 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `SurfaceData prepareShadingData(VertexData, float3, uint)` | 准备着色数据 |
| `float3 evalBSDFSlice(float2, uint, ITextureSampler, inout SampleGenerator)` | 评估指定材质 ID 的双向散射分布函数切片 |
| `void execute(uint2)` | 入口函数：确定视口 ID（0=初始, 1=差异, 2=参考），评估对应内容 |

## 依赖关系 / import

- `Utils/Math/MathConstants.slangh` — 数学常量
- `Scene.Shading` — 场景着色
- `Scene.Material.ShadingUtils` — 着色工具
- `Utils.Sampling.SampleGenerator` — 采样生成器
- `Utils.Math.BitTricks` — 位操作
- `Utils.Math.MathHelpers` — 数学辅助
- `BSDFOptimizerParams` — 优化器参数
- `BSDFOptimizerHelpers` — 辅助函数

## 实现细节

- **三视口判定**: 根据像素 X 坐标确定属于哪个视口：X >= refViewPortOffset.x 为参考视口，X >= diffViewPortOffset.x 为差异视口，否则为初始视口。
- **差异计算**: 中间视口显示 `abs(fInitVal - fRefVal)`，即两个双向散射分布函数的逐像素绝对差值。
- **非可微评估**: 与 BSDFOptimizer.cs.slang 不同，此着色器使用普通（非可微）的 `mi.eval()` 进行评估，仅用于可视化。
- **余弦项移除**: 使用绝对值 `abs(dot(N, wo))` 处理 NdotL，避免背面问题。
