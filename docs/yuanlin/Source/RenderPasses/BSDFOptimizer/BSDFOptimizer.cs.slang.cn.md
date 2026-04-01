# BSDFOptimizer.cs.slang 源码文档

> 路径: `Source/RenderPasses/BSDFOptimizer/BSDFOptimizer.cs.slang`
> 类型: 计算着色器 (Slang)
> 模块: RenderPasses/BSDFOptimizer

## 功能概述

BSDFOptimizer 的梯度计算着色器。使用 Slang 的自动微分功能，通过反向传播计算双向散射分布函数参数的梯度。在双向散射分布函数切片空间中比较初始材质和参考材质，计算 L2 损失并反向传播梯度。

## 结构体与接口

### `SurfaceData`（可微）

实现 `IDifferentiable` 接口。

| 字段 | 类型 | 说明 |
|------|------|------|
| `sd` | `ShadingData` | 着色数据 |
| `wo` | `float3` | 出射方向 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `[Differentiable] SurfaceData prepareShadingData(VertexData, float3, uint)` | 可微着色数据准备 |
| `[Differentiable] IMaterialInstance getDiffMaterialInstance(out DiffMaterialData, ShadingData)` | 获取可微材质实例 |
| `[Differentiable] float3 evalBSDFSlice(bool isRef, uint2)` | 可微双向散射分布函数切片评估，isRef 控制参考/初始材质 |
| `[Differentiable] float3 computeLoss(uint2)` | 可微 L2 损失计算：0.5 * (fCur - fRef)^2 |
| `void execute(uint2)` | 执行入口：调用 `bwd_diff(computeLoss)` 反向传播梯度 |

## 依赖关系 / import

- `Utils/Math/MathConstants.slangh` — 数学常量
- `Scene.Shading` — 场景着色
- `Scene.Material.ShadingUtils` — 着色工具
- `Utils.Sampling.SampleGenerator` — 采样生成器
- `Utils.Math.BitTricks` — 位操作
- `Utils.Math.MathHelpers` — 数学辅助
- `BSDFOptimizerParams` — 参数定义
- `BSDFOptimizerHelpers` — 辅助函数（视口坐标、切片几何）

## 实现细节

- **反向微分**: 使用 `bwd_diff(computeLoss)(pixel, float3(1.f))` 进行反向传播，输出梯度为全 1 向量。
- **可微/非可微分支**: 参考材质使用 `no_diff mi.eval()`（不传播梯度），初始材质使用 `mi.evalAD()`（传播梯度到材质参数）。
- **材质梯度偏移**: `sd.materialGradOffset = 0`，`sd.threadID = 0`，将梯度写入固定位置。
- **余弦项移除**: 评估双向散射分布函数后除以 NdotL，使切片仅显示纯双向散射分布函数值。
- **线程组**: 16x16 线程组，在 `bsdfTableDim` 范围内执行。
