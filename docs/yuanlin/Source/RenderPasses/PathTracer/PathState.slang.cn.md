# PathState.slang 源码文档

> 路径: `Source/RenderPasses/PathTracer/PathState.slang`
> 类型: Slang 着色器
> 模块: RenderPasses/PathTracer

## 功能概述

定义路径追踪器中路径的实时状态结构体 `PathState`。该结构体记录了路径追踪过程中每条路径的完整状态信息，包括路径标志、顶点索引、反弹计数器、散射光线参数、路径通量、累积贡献以及降噪引导数据等。

## 结构体与接口

### `PathFlags`（枚举）

| 标志 | 说明 |
|------|------|
| `active` | 路径是否活跃 |
| `hit` | 散射光线是否命中 |
| `transmission` | 是否经历透射事件 |
| `specular` | 是否经历镜面事件 |
| `delta` | 是否经历 Delta 事件 |
| `insideDielectricVolume` | 是否在电介质体积内 |
| `lightSampledUpper/Lower` | 是否通过 NEE 采样了光源 |
| `diffusePrimaryHit` | 主命中是否为漫反射 |
| `specularPrimaryHit` | 主命中是否为镜面反射 |
| `deltaReflectionPrimaryHit` | 主命中是否为 Delta 反射 |
| `deltaTransmissionPath` | 是否为 Delta 透射路径 |
| `deltaOnlyPath` | 路径是否只经历 Delta 事件 |

### `BounceType`（枚举）

| 值 | 说明 |
|----|------|
| `Diffuse` | 漫反射 |
| `Specular` | 镜面反射（含 Delta） |
| `Transmission` | 透射 |

### `PathState`

| 成员 | 类型 | 说明 |
|------|------|------|
| `id` | `uint` | 路径 ID，编码像素坐标和样本索引 |
| `flagsAndVertexIndex` | `uint` | 路径标志（高位）和顶点索引（低 10 位）的合并存储 |
| `rejectedHits` | `uint16_t` | 已拒绝的伪交点数量 |
| `sceneLength` | `float16_t` | 场景空间路径长度 |
| `bounceCounters` | `uint` | 打包的各类反弹计数器 |
| `origin` / `dir` | `float3` | 散射光线的原点和方向 |
| `pdf` | `float` | 散射光线的概率密度 |
| `normal` | `float3` | 散射光线原点处的着色法线 |
| `hit` | `HitInfo` | 命中信息 |
| `thp` | `float3` | 路径通量 |
| `L` | `float3` | 累积路径贡献 |
| `guideData` | `GuideData` | 降噪引导数据 |
| `interiorList` | `InteriorList` | 内部材质栈 |
| `sg` | `SampleGenerator` | 采样生成器状态 |

## 依赖关系 / import

- `Utils.Math.PackedFormats` — 打包格式
- `Scene.HitInfo` — 命中信息
- `Utils.Math.Ray` — 光线定义
- `Utils.Sampling.SampleGenerator` — 采样生成器
- `Rendering.Materials.InteriorList` — 内部材质列表
- `GuideData` — 降噪引导数据
