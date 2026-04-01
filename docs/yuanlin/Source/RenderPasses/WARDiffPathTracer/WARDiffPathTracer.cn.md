# WARDiffPathTracer 源码文档

> 路径: `Source/RenderPasses/WARDiffPathTracer/WARDiffPathTracer.h` + `Source/RenderPasses/WARDiffPathTracer/WARDiffPathTracer.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/WARDiffPathTracer

## 功能概述

WARDiffPathTracer（Warped-Area Reparameterized Differentiable Path Tracer）是一个基于弯曲区域重参数化的可微路径追踪渲染通道。它使用 DXR 1.1 的 TraceRayInline 实现可微渲染，能够处理几何不连续性带来的梯度问题。支持前向模式和反向模式自动微分，可用于逆向渲染优化。

## 类与接口

### `WARDiffPathTracer`

- **继承**: `RenderPass`
- **职责**: 实现带弯曲区域重参数化的可微路径追踪器

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `WARDiffPathTracer(ref<Device>, const Properties&)` | 构造函数，解析属性，创建采样生成器和像素调试工具 |
| `Properties getProperties() const override` | 序列化所有渲染、微分、采样和 WAR 参数 |
| `RenderPassReflection reflect(const CompileData&) override` | 声明输出通道 `color` 和 `dColor`（梯度） |
| `void setScene(RenderContext*, const ref<Scene>&) override` | 加载场景，重置光照和追踪通道 |
| `void execute(RenderContext*, const RenderData&) override` | 执行帧：更新程序、准备资源、运行追踪通道 |
| `void renderUI(Gui::Widgets&) override` | 渲染 UI：渲染选项和调试选项 |
| `void registerBindings(pybind11::module&)` | 注册 Python 绑定：scene_gradients、run_backward、dL_dI 等 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mParams` | `WARDiffPathTracerParams` | 运行时路径追踪参数 |
| `mStaticParams` | `StaticParams` | 编译时静态参数 |
| `mDiffDebugParams` | `DiffDebugParams` | 可微渲染调试参数 |
| `mInvOptParams` | `InverseOptimizationParams` | 逆向优化参数 |
| `mpScene` | `ref<Scene>` | 当前场景 |
| `mpSampleGenerator` | `ref<SampleGenerator>` | 采样生成器 |
| `mpEmissiveSampler` | `unique_ptr<EmissiveLightSampler>` | 发光采样器（均匀采样） |
| `mpSceneGradients` | `ref<SceneGradients>` | 场景梯度存储 |
| `mpdLdI` | `ref<Buffer>` | 损失函数对图像像素值的导数 |
| `mpDiffPTBlock` | `ref<ParameterBlock>` | 可微路径追踪参数块 |

### `StaticParams`（内部结构）

编译时静态参数，修改需要重新编译着色器。

| 成员 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `samplesPerPixel` | `uint32_t` | 1 | 每像素采样数 |
| `maxBounces` | `uint32_t` | 0 | 最大间接反弹次数 |
| `diffMode` | `DiffMode` | `ForwardDiffDebug` | 微分模式 |
| `diffVarName` | `string` | `""` | 微分变量名称 |
| `useBSDFSampling` | `bool` | true | 使用双向散射分布函数重要性采样 |
| `useNEE` | `bool` | true | 使用下一事件估计 |
| `useMIS` | `bool` | true | 使用多重重要性采样 |
| `useWAR` | `bool` | true | 使用弯曲区域重参数化 |
| `auxSampleCount` | `uint32_t` | 16 | 辅助采样数 |
| `log10vMFConcentration` | `float` | 5.0 | vMF 分布集中度参数（log10） |
| `useAntitheticSampling` | `bool` | true | 使用对偶采样减少方差 |

### `TracePass`（内部结构）

| 成员 | 类型 | 说明 |
|------|------|------|
| `name` | `string` | 通道名称 |
| `passDefine` | `string` | 通道宏定义 |
| `pProgram` | `ref<Program>` | 光线追踪程序 |
| `pBindingTable` | `ref<RtBindingTable>` | 绑定表 |
| `pVars` | `ref<RtProgramVars>` | 程序变量 |

## 依赖关系

### 本文件引用

- `Falcor.h` — 核心头文件
- `RenderGraph/RenderPass.h` — 渲染通道基类
- `Utils/Sampling/SampleGenerator.h` — 采样生成器
- `Utils/Debug/PixelDebug.h` — 像素调试工具
- `Rendering/Lights/EmissiveUniformSampler.h` — 均匀发光采样器
- `DiffRendering/SceneGradients.h` — 场景梯度
- `DiffRendering/SharedTypes.slang` — 可微渲染共享类型
- `Params.slang` — 参数定义

### 被以下文件引用

- `WARDiffPathTracer.rt.slang` — 光线追踪着色器

## 实现细节

- **微分模式**: 支持四种模式——Primal（原始渲染）、BackwardDiff（反向微分）、ForwardDiffDebug（前向微分调试）、BackwardDiffDebug（反向微分调试）。
- **预配置场景**: 内置 `CBOX_BUNNY_MATERIAL`（材质反照率微分）和 `CBOX_BUNNY_TRANSLATION`（几何平移微分）两种调试配置。
- **发光采样**: 仅使用均匀发光采样器（非 LightBVH），避免 Cornell Box 场景的已知问题。
- **不支持环境光**: 当前不支持环境贴图光源。
- **Python 接口**: 暴露 `scene_gradients`、`run_backward`、`dL_dI` 属性和 `set_mesh_to_optimize` 方法。
