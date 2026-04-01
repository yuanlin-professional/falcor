# PTUtils.slang 源码文档

> 路径: `Source/RenderPasses/WARDiffPathTracer/PTUtils.slang`
> 类型: Slang 着色器工具库
> 模块: RenderPasses/WARDiffPathTracer

## 功能概述

WARDiffPathTracer 的路径追踪核心工具库。包含路径数据结构、光源采样、双向散射分布函数评估、下一事件估计（NEE）和命中处理等功能。所有关键函数均标记为 `[Differentiable]`，支持自动微分。

## 结构体与接口

### `PathData`（可微）

路径追踪状态数据，实现 `IDifferentiable` 接口。

| 字段 | 类型 | 说明 |
|------|------|------|
| `radiance` | `float3` | 路径累积辐射度 |
| `thp` | `float3` | 路径吞吐量 |
| `length` | `uint` | 路径长度 |
| `terminated` | `bool` | 路径是否终止 |
| `normal` | `float3` | MIS 用法线（no_diff） |
| `pdf` | `float` | MIS 用采样 pdf（no_diff） |

### `LightSample`

光源采样结果。

| 字段 | 类型 | 说明 |
|------|------|------|
| `Li` | `float3` | 入射辐射度（已除以 pdf） |
| `pdf` | `float` | 立体角 pdf |
| `origin` | `float3` | 阴影光线起点（已偏移） |
| `distance` | `float` | 可见性测试距离 |
| `dir` | `float3` | 可见性测试方向 |
| `lightPos` | `float3` | 光源位置（用于可微评估） |
| `lightNormal` | `float3` | 光源法线（用于可微评估） |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float evalMIS(float, float, float, float)` | 幂二启发式 MIS 权重计算 |
| `[Differentiable] ShadingData loadShadingData(...)` | 可微加载着色数据，覆盖法线和切线以启用自动微分 |
| `[Differentiable] IMaterialInstance getDiffMaterialInstance(...)` | 获取可微材质实例 |
| `[Differentiable] bool generateScatterRay(...)` | 可微散射光线生成，可选 WAR 重参数化 |
| `bool generateEmissiveSample(...)` | 在发光几何体上生成光源采样（假定静态面积光） |
| `[Differentiable] void computeNEE(...)` | 可微下一事件估计，计算直接光照贡献 |
| `[Differentiable] void handleHit(...)` | 可微命中处理：加载着色数据、评估发光、NEE、生成散射光线 |

## 依赖关系 / import

- `Scene/SceneDefines.slangh` — 场景宏定义
- `Utils/Math/MathConstants.slangh` — 数学常量
- `Rendering.Lights.EmissiveLightSampler` — 发光采样器
- `Rendering.Lights.EmissiveLightSamplerHelpers` — 采样辅助
- `Rendering.Lights.LightHelpers` — 光源辅助
- `Scene.Raytracing` — 光线追踪
- `Scene.Intersection` — 几何交叉
- `Scene.RaytracingInline` — 内联光线追踪
- `Utils.Sampling.SampleGenerator` — 采样生成器
- `DiffRendering.SharedTypes` — 共享类型
- `DiffRendering.DiffSceneIO` — 可微场景 I/O
- `DiffRendering.DiffSceneQuery` — 可微场景查询
- `StaticParams` — 静态参数（导出）
- `Params` — 运行时参数（导出）
- `WarpedAreaReparam` — 弯曲区域重参数化

## 实现细节

- **可微材质评估**: 使用 `mi.evalAD(diffData, sd, dir, sg)` 进行可微双向散射分布函数评估，支持梯度反向传播到材质参数。
- **MIS 支持**: 在 NEE 中使用幂二启发式将光源采样和双向散射分布函数采样结合。
- **发光 MIS**: 在 `handleHit` 中对非主命中的发光体应用 MIS 权重。
- **材质梯度偏移**: 通过 `sd.materialGradOffset` 设置材质梯度写入偏移。
- **`[PreferRecompute]`**: 标记函数优先重算而非缓存中间结果，节省内存但可能增加计算量。
- **仅三角形支持**: 当前仅支持三角形命中类型。
