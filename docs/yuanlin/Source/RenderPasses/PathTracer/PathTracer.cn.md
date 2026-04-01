# PathTracer 源码文档

> 路径: `Source/RenderPasses/PathTracer/PathTracer.h` + `Source/RenderPasses/PathTracer/PathTracer.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/PathTracer

## 功能概述

PathTracer 是 Falcor 框架中的核心路径追踪渲染通道实现。该文件实现了一个高性能的参考级路径追踪器，支持多种采样策略（双向散射分布函数采样、下一事件估计 NEE、多重重要性采样 MIS）、RTXDI 直接光照采样、NRD 降噪数据输出以及着色器执行重排序（SER）等高级特性。整个渲染流程分为路径生成、光线追踪和样本解析三个主要阶段。

## 类与接口

### `PathTracer`

- **继承**: `RenderPass`
- **职责**: 管理路径追踪渲染通道的完整生命周期，包括场景设置、着色器编译、资源管理、参数配置及 GPU 调度执行。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `PathTracer(ref<Device>, const Properties&)` | 构造函数，解析属性并初始化路径追踪器 |
| `reflect(const CompileData&)` | 声明渲染通道的输入/输出端口 |
| `setScene(RenderContext*, const ref<Scene>&)` | 设置场景，初始化光源采样器和相关资源 |
| `execute(RenderContext*, const RenderData&)` | 执行路径追踪的主入口，依次调用 beginFrame、generatePaths、tracePass、resolvePass、endFrame |
| `renderUI(Gui::Widgets&)` | 渲染用户界面，包括渲染参数、调试选项和统计信息 |
| `generatePaths(RenderContext*, const RenderData&)` | 启动路径生成计算着色器，处理主命中点 |
| `tracePass(RenderContext*, const RenderData&, TracePass&)` | 执行光线追踪通道（散射光线） |
| `resolvePass(RenderContext*, const RenderData&)` | 执行样本解析通道，对多样本像素进行平均 |
| `prepareLighting(RenderContext*)` | 准备光照资源（环境贴图采样器、发光体采样器） |
| `prepareRTXDI(RenderContext*)` | 准备 RTXDI 直接光照采样资源 |
| `bindShaderData(const ShaderVar&, const RenderData&, bool)` | 绑定着色器数据到 GPU |
| `setNRDData(const ShaderVar&, const RenderData&)` | 设置 NRD 降噪相关数据 |
| `reset()` | 重置路径追踪器状态 |
| `registerBindings(pybind11::module&)` | 注册 Python 绑定 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mParams` | `PathTracerParams` | 运行时路径追踪参数 |
| `mStaticParams` | `StaticParams` | 静态参数，作为编译时着色器常量 |
| `mpScene` | `ref<IScene>` | 当前场景引用 |
| `mpSampleGenerator` | `ref<SampleGenerator>` | GPU 伪随机样本生成器 |
| `mpEnvMapSampler` | `unique_ptr<EnvMapSampler>` | 环境贴图采样器 |
| `mpEmissiveSampler` | `unique_ptr<EmissiveLightSampler>` | 发光体光源采样器 |
| `mpRTXDI` | `unique_ptr<RTXDI>` | RTXDI 直接光照采样器 |
| `mpPixelStats` | `unique_ptr<PixelStats>` | 像素统计工具 |
| `mpPixelDebug` | `unique_ptr<PixelDebug>` | 像素调试工具 |
| `mpGeneratePaths` | `ref<ComputePass>` | 路径生成计算通道 |
| `mpResolvePass` | `ref<ComputePass>` | 样本解析计算通道 |
| `mpTracePass` | `unique_ptr<TracePass>` | 主光线追踪通道 |
| `mpTraceDeltaReflectionPass` | `unique_ptr<TracePass>` | Delta 反射追踪通道（NRD 用） |
| `mpTraceDeltaTransmissionPass` | `unique_ptr<TracePass>` | Delta 透射追踪通道（NRD 用） |
| `mOutputNRDData` | `bool` | 是否输出 NRD 降噪数据 |
| `mOutputGuideData` | `bool` | 是否输出降噪引导数据 |

### `PathTracer::TracePass`（内部结构体）

- **职责**: 封装单个光线追踪通道的程序、绑定表和变量。

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `name` | `std::string` | 通道名称 |
| `passDefine` | `std::string` | 通道对应的宏定义 |
| `pProgram` | `ref<Program>` | 光线追踪程序 |
| `pBindingTable` | `ref<RtBindingTable>` | 光线追踪绑定表 |
| `pVars` | `ref<RtProgramVars>` | 程序变量 |

### `PathTracer::StaticParams`（内部结构体）

- **职责**: 定义编译时静态参数，更改这些参数需要重新编译着色器。

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `samplesPerPixel` | `uint32_t` | 每像素采样数 |
| `maxSurfaceBounces` | `uint32_t` | 最大表面反弹次数 |
| `maxDiffuseBounces` | `uint32_t` | 最大漫反射反弹次数 |
| `maxSpecularBounces` | `uint32_t` | 最大镜面反射反弹次数 |
| `maxTransmissionBounces` | `uint32_t` | 最大透射反弹次数 |
| `useBSDFSampling` | `bool` | 是否使用双向散射分布函数重要性采样 |
| `useNEE` | `bool` | 是否使用下一事件估计 |
| `useMIS` | `bool` | 是否使用多重重要性采样 |
| `useRTXDI` | `bool` | 是否使用 RTXDI |
| `useRussianRoulette` | `bool` | 是否使用俄罗斯轮盘赌终止低通量路径 |
| `useSER` | `bool` | 是否启用着色器执行重排序 |
| `colorFormat` | `ColorFormat` | 内部颜色格式 |
| `useNRDDemodulation` | `bool` | 是否启用 NRD 解调 |

## 依赖关系

### 本文件引用

- `Falcor.h` — 框架核心头文件
- `RenderGraph/RenderPass.h` — 渲染通道基类
- `Utils/Debug/PixelDebug.h` — 像素调试工具
- `Utils/Sampling/SampleGenerator.h` — 样本生成器
- `Rendering/Lights/LightBVHSampler.h` — 光源 BVH 采样器
- `Rendering/Lights/EmissivePowerSampler.h` — 发光体功率采样器
- `Rendering/Lights/EnvMapSampler.h` — 环境贴图采样器
- `Rendering/Utils/PixelStats.h` — 像素统计
- `Rendering/RTXDI/RTXDI.h` — RTXDI 模块
- `Params.slang` — 路径追踪参数定义

### 被以下文件引用

- `GeneratePaths.cs.slang` — 路径生成计算着色器
- `TracePass.rt.slang` — 光线追踪着色器
- `ResolvePass.cs.slang` — 样本解析着色器
- `ReflectTypes.cs.slang` — 类型反射辅助

## 实现细节

- 渲染流程按以下顺序执行：`beginFrame` → `generatePaths` → `tracePass`（主通道 + 可选 Delta 通道）→ `resolvePass` → `endFrame`
- 支持固定和可变的每像素采样数，可变采样数通过输入纹理指定
- 使用基于 Tile 的内存布局（16x16 像素），Tile 内像素按 Morton 序排列
- 光源采样支持 LightBVH 和 EmissivePower 两种采样器
- NRD 降噪数据输出分为漫反射、镜面反射、Delta 反射和 Delta 透射四个通道
- 支持 SER（着色器执行重排序）以提升 GPU 线程一致性
