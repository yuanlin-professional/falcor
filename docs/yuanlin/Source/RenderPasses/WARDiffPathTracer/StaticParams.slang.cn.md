# StaticParams.slang 源码文档

> 路径: `Source/RenderPasses/WARDiffPathTracer/StaticParams.slang`
> 类型: Slang 着色器配置文件
> 模块: RenderPasses/WARDiffPathTracer

## 功能概述

定义 WARDiffPathTracer 的编译时静态常量。这些常量由主机端通过宏定义注入，在着色器编译时确定，控制路径追踪的核心行为参数。

## 结构体与接口

### 静态常量

| 常量 | 对应宏定义 | 说明 |
|------|-----------|------|
| `kSamplesPerPixel` | `SAMPLES_PER_PIXEL` | 每像素采样数 |
| `kMaxBounces` | `MAX_BOUNCES` | 最大反弹次数 |
| `kDiffMode` | `DIFF_MODE` | 微分模式（Primal/BackwardDiff/ForwardDiffDebug/BackwardDiffDebug） |
| `kUseBSDFSampling` | `USE_BSDF_SAMPLING` | 是否使用双向散射分布函数采样 |
| `kUseNEE` | `USE_NEE` | 是否使用下一事件估计 |
| `kUseMIS` | `USE_MIS` | 是否使用多重重要性采样 |
| `kUseWAR` | `USE_WAR` | 是否使用弯曲区域重参数化 |
| `kAuxSampleCount` | `AUX_SAMPLE_COUNT` | 辅助采样数量 |
| `kLog10vMFConcentration` | `LOG10_VMF_CONCENTRATION` | vMF 分布集中度（log10） |
| `kLog10vMFConcentrationScreen` | `LOG10_VMF_CONCENTRATION_SCREEN` | 屏幕空间 vMF 集中度 |
| `kBoundaryTermBeta` | `BOUNDARY_TERM_BETA` | 边界项 Beta 参数 |
| `kUseAntitheticSampling` | `USE_ANTITHETIC_SAMPLING` | 是否使用对偶采样 |
| `kHarmonicGamma` | `HARMONIC_GAMMA` | 调和权重 Gamma 参数 |
| `kUseEnvLight` | `USE_ENV_LIGHT` | 是否使用环境光 |
| `kUseAnalyticLights` | `USE_ANALYTIC_LIGHTS` | 是否使用解析光源 |
| `kUseEmissiveLights` | `USE_EMISSIVE_LIGHTS` | 是否使用发光光源 |

## 依赖关系 / import

- `DiffRendering.SharedTypes` — 可微渲染共享类型（导出 DiffMode 枚举等）

## 实现细节

- 文件开头检查 `SAMPLES_PER_PIXEL` 宏是否定义，未定义时产生编译错误，确保主机端正确设置所有宏定义。
- 所有常量使用 `static const` 声明，在编译时被内联优化。
