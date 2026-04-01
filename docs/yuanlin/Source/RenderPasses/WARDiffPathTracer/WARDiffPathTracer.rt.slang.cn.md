# WARDiffPathTracer.rt.slang 源码文档

> 路径: `Source/RenderPasses/WARDiffPathTracer/WARDiffPathTracer.rt.slang`
> 类型: 光线追踪着色器 (Slang)
> 模块: RenderPasses/WARDiffPathTracer

## 功能概述

WARDiffPathTracer 的核心光线追踪着色器。实现了可微路径追踪的主入口，支持原始渲染、前向微分和反向微分三种模式。使用高斯像素滤波器和弯曲区域重参数化处理主光线。

## 结构体与接口

### `DiffPTData`

可微路径追踪数据块，作为 ParameterBlock 绑定。

| 字段 | 类型 | 说明 |
|------|------|------|
| `params` | `WARDiffPathTracerParams` | 路径追踪运行时参数 |
| `emissiveSampler` | `EmissiveLightSampler` | 发光采样器 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float2 sampleGaussian(float2, float)` | 将均匀样本转换为高斯样本（Box-Muller 变换） |
| `[Differentiable] float evalGaussian(float2, float)` | 可微的高斯函数评估 |
| `[Differentiable] float3 tracePath(SceneQueryAD, float2, float2, inout SampleGenerator)` | 可微路径追踪：WAR 重参数化主光线 + 高斯像素滤波 + 循环追踪 |
| `[Differentiable] float3 tracePaths(SceneQueryAD, uint2)` | 对偶采样包装器，处理采样生成和抖动 |
| `void rayGen()` | 光线生成入口，根据微分模式分支执行 |

## 依赖关系 / import

- `Utils/Math/MathConstants.slangh` — 数学常量
- `Rendering.Lights.EmissiveLightSampler` — 发光采样器
- `Scene.Raytracing` — 光线追踪接口
- `Scene.RaytracingInline` — 内联光线追踪
- `Utils.Sampling.SampleGenerator` — 采样生成器
- `Utils.Debug.PixelDebug` — 像素调试
- `DiffRendering.SharedTypes` — 可微渲染共享类型
- `DiffRendering.DiffSceneIO` — 可微场景 I/O
- `DiffRendering.DiffSceneQuery` — 可微场景查询
- `DiffRendering.DiffDebugParams` — 微分调试参数
- `DiffRendering.InverseOptimizationParams` — 逆向优化参数
- `PTUtils` — 路径追踪工具函数
- `WarpedAreaReparam` — 弯曲区域重参数化

## 实现细节

- **四种微分模式**:
  - `Primal`: 普通渲染，输出颜色
  - `BackwardDiff`: 反向微分，先前向渲染存图，再反向传播 dL/dI 梯度
  - `ForwardDiffDebug`: 前向微分可视化，输出颜色和梯度（仅红色通道）
  - `BackwardDiffDebug`: 反向微分调试，使用 float3(1,0,0) 作为输出梯度
- **高斯像素滤波**: sigma=0.5 的高斯滤波器，通过 `evalGaussian` 计算权重并除以 detach 后的权重以保持归一化
- **对偶采样**: 偶数帧和奇数帧使用相反的抖动方向，减少梯度估计方差
- **梯度钳位**: 前向微分模式下将梯度值钳位到 `assertThreshold` 范围内
