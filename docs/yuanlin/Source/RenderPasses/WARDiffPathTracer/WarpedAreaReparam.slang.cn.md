# WarpedAreaReparam.slang 源码文档

> 路径: `Source/RenderPasses/WARDiffPathTracer/WarpedAreaReparam.slang`
> 类型: Slang 着色器库
> 模块: RenderPasses/WARDiffPathTracer

## 功能概述

实现弯曲区域重参数化（Warped-Area Reparameterization, WAR）算法的核心着色器代码。该技术通过对光线方向进行重参数化，使得渲染积分在几何不连续处（如物体边缘）也能产生正确的梯度。包含二次光线和主光线（屏幕空间）两种重参数化方法。

## 结构体与接口

无独立结构体定义。

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float computeBoundaryTerm(float3, float3)` | 计算边界项 (dot(N,D))^2 |
| `[Differentiable] float computeHarmonicWeight(...)` | 计算调和权重，用于辅助样本的加权平均 |
| `[Differentiable] DifferentialPair<float> fwd_computeHarmonicWeight(...)` | `computeHarmonicWeight` 的自定义前向导数 |
| `float3 _reflect(float3, float3)` | 反射函数包装器 |
| `[Differentiable] bool traceAsymptoticWeightedMeanIntersection<N>(...)` | 泛型函数：追踪 N 个方向的渐近加权平均交点 |
| `[Differentiable] void computeWarpedRay(...)` | 计算弯曲光线（3 方向，用于雅可比行列式） |
| `[Differentiable] float reparameterizeRay(...)` | 二次光线重参数化，返回雅可比行列式 |
| `[Differentiable] void computeWarpedPrimarySample(...)` | 计算弯曲主采样（屏幕空间，2 方向） |
| `[Differentiable] float reparameterizeScreenSample(...)` | 屏幕空间主光线重参数化，返回雅可比行列式 |

## 依赖关系 / import

- `Utils/Math/MathConstants.slangh` — 数学常量
- `Scene.RaytracingInline` — 内联光线追踪
- `Utils.Sampling.SampleGenerator` — 采样生成器
- `Utils.Math.MathHelpers` — 数学辅助
- `Utils.Math.DiffMathHelpers` — 可微数学辅助
- `Utils.Math.MatrixUtils` — 矩阵工具（行列式计算）
- `Utils.Debug.PixelDebug` — 像素调试
- `DiffRendering.SharedTypes` — 共享类型
- `DiffRendering.DiffSceneIO` — 可微场景 I/O
- `DiffRendering.DiffSceneQuery` — 可微场景查询
- `StaticParams` — 静态参数
- `Params` — 运行时参数

## 实现细节

- **von Mises-Fisher (vMF) 采样**: 使用 vMF 分布在基准方向周围生成辅助光线方向，集中度参数 kappa 通过 `kLog10vMFConcentration` 控制。
- **对偶采样**: 当 `kUseAntitheticSampling` 启用时，每两个辅助样本为一对，第二个样本关于基准方向反射，减少方差。
- **泛型 N 方向**: `traceAsymptoticWeightedMeanIntersection` 使用 Slang 泛型参数 `<let N : int>`，二次光线用 N=3（用于 3x3 雅可比），主光线用 N=2（用于 2x2 雅可比）。
- **调和权重**: 权重公式包含逆 vMF 密度、边界项和立方衰减，自定义前向导数 `fwd_computeHarmonicWeight` 用于高效梯度计算。
- **雅可比行列式**: 通过 3 个（或 2 个）方向的前向微分计算弯曲映射的雅可比矩阵，取其行列式作为重参数化权重。
- **梯度隔离**: 在雅可比计算时使用 `GradientMode.None` 禁止场景梯度的传播，确保仅计算弯曲映射本身的导数。
- **交点附着**: 使用 `DiffIntersectionMode.AttachToGeometry` 将交点微分附着到几何体上，使得交点位置的梯度能传播到场景参数。
