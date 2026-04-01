# PathTracer.slang 源码文档

> 路径: `Source/RenderPasses/PathTracer/PathTracer.slang`
> 类型: Slang 着色器
> 模块: RenderPasses/PathTracer

## 功能概述

路径追踪器的核心 GPU 着色器逻辑实现。定义了 `PathTracer` 结构体，包含路径生成、命中处理、光源采样（NEE）、散射光线生成、多重重要性采样（MIS）以及输出写入等完整路径追踪管线。该文件是路径追踪算法的主体实现，处理所有路径事件和采样策略。

## 结构体与接口

### `PathTracer`

包含路径追踪器的所有 GPU 端资源绑定和核心方法。

#### 关键资源成员

| 成员 | 说明 |
|------|------|
| `params` | 运行时参数 |
| `vbuffer` | 主命中可见性缓冲区 |
| `outputColor` | 输出颜色纹理 |
| `sampleColor` | 每样本颜色结构化缓冲区 |
| `sampleGuideData` | 每样本降噪引导数据 |
| `outputNRD` | NRD 降噪输出缓冲区 |

## 函数

| 函数 | 说明 |
|------|------|
| `generatePath(uint pathID, inout PathState)` | 从路径 ID 生成初始路径状态 |
| `handleHit(inout PathState, IVisibilityQuery)` | 处理路径命中，包括材质评估、NEE、散射光线生成 |
| `handleMiss(inout PathState)` | 处理路径未命中（评估环境光） |
| `evalDirect/evalNEE(...)` | 评估直接光照和下一事件估计 |
| `generateScatterRay(ShadingData, IMaterialInstance, inout PathState)` | 生成散射光线 |
| `evalMIS(...)` | 多重重要性采样权重计算 |
| `handleNestedDielectrics(...)` | 处理嵌套电介质 |
| `writeOutput(PathState)` | 将路径结果写入输出缓冲区 |
| `setupPathLogging(PathState)` | 设置路径调试日志 |
| `getCoherenceHints(...)` | 获取 SER 一致性提示 |

## 依赖关系 / import

- `Rendering.Lights.EnvMapSampler` — 环境贴图采样器
- `Rendering.Lights.EmissiveLightSampler` — 发光体采样器
- `Rendering.RTXDI.RTXDI` — RTXDI 模块
- `Rendering.Materials.*` — 材质系统
- `Utils.Sampling.*` — 采样工具
- `LoadShadingData`, `PathState`, `ColorType`, `Params`, `GuideData` — 本模块类型
