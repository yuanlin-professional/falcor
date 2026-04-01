# StaticParams.slang 源码文档

> 路径: `Source/RenderPasses/PathTracer/StaticParams.slang`
> 类型: Slang 着色器
> 模块: RenderPasses/PathTracer

## 功能概述

将主机端设置的宏定义（#define）转换为编译时着色器常量，用于无动态分支开销地配置路径追踪器。这些常量最终将被 Slang 特化常量替代。

## 结构体与接口

### 编译时常量

| 常量 | 来源宏 | 说明 |
|------|--------|------|
| `kUseViewDir` | `USE_VIEW_DIR` | 是否使用自定义视线方向 |
| `kSamplesPerPixel` | `SAMPLES_PER_PIXEL` | 每像素采样数 |
| `kMaxSurfaceBounces` | `MAX_SURFACE_BOUNCES` | 最大表面反弹次数 |
| `kMaxDiffuseBounces` | `MAX_DIFFUSE_BOUNCES` | 最大漫反射反弹次数 |
| `kMaxSpecularBounces` | `MAX_SPECULAR_BOUNCES` | 最大镜面反射反弹次数 |
| `kMaxTransmissionBounces` | `MAX_TRANSMISSON_BOUNCES` | 最大透射反弹次数 |
| `kAdjustShadingNormals` | `ADJUST_SHADING_NORMALS` | 是否调整着色法线 |
| `kUseBSDFSampling` | `USE_BSDF_SAMPLING` | 是否使用双向散射分布函数采样 |
| `kUseNEE` | `USE_NEE` | 是否使用下一事件估计 |
| `kUseMIS` | `USE_MIS` | 是否使用多重重要性采样 |
| `kUseRussianRoulette` | `USE_RUSSIAN_ROULETTE` | 是否使用俄罗斯轮盘赌 |
| `kUseRTXDI` | `USE_RTXDI` | 是否使用 RTXDI |
| `kUseAlphaTest` | `USE_ALPHA_TEST` | 是否使用 Alpha 测试 |
| `kPrimaryLodMode` | `PRIMARY_LOD_MODE` | 主命中纹理 LOD 模式 |
| `kUseNRDDemodulation` | `USE_NRD_DEMODULATION` | 是否使用 NRD 解调 |
| `kColorFormat` | `COLOR_FORMAT` | 颜色格式 |
| `kMISHeuristic` | `MIS_HEURISTIC` | MIS 启发式类型 |

## 依赖关系 / import

- `Rendering.Materials.TexLODTypes` — 纹理 LOD 类型定义
