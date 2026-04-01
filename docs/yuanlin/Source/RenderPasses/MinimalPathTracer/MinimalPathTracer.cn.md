# MinimalPathTracer 源码文档

> 路径: `Source/RenderPasses/MinimalPathTracer/MinimalPathTracer.h` + `Source/RenderPasses/MinimalPathTracer/MinimalPathTracer.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/MinimalPathTracer

## 功能概述

MinimalPathTracer 是一个最小化的暴力路径追踪渲染通道。该通道故意不使用任何重要性采样或方差缩减技术，旨在生成无偏/一致的真值图像，用于验证其他更复杂渲染器的正确性。当前不支持透射和嵌套电介质。

## 类与接口

### `MinimalPathTracer`

- **继承**: `RenderPass`
- **职责**: 实现最小化路径追踪器，生成真值参考图像

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `MinimalPathTracer(ref<Device>, const Properties&)` | 构造函数，解析属性并创建采样生成器 |
| `Properties getProperties() const override` | 序列化最大反弹次数、直接光照、重要性采样等配置 |
| `RenderPassReflection reflect(const CompileData&) override` | 声明输入通道（vbuffer, viewW）和输出通道（color） |
| `void execute(RenderContext*, const RenderData&) override` | 设置着色器宏定义、绑定资源、派发光线追踪 |
| `void renderUI(Gui::Widgets&) override` | 渲染 UI 控件：最大反弹数、直接光照开关、重要性采样开关 |
| `void setScene(RenderContext*, const ref<Scene>&) override` | 加载场景，创建光线追踪程序和着色器绑定表（SBT） |
| `void prepareVars()` | 准备程序变量，绑定采样生成器 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpScene` | `ref<Scene>` | 当前加载的场景 |
| `mpSampleGenerator` | `ref<SampleGenerator>` | GPU 采样生成器（均匀分布） |
| `mMaxBounces` | `uint` | 最大间接反弹次数，默认 3 |
| `mComputeDirect` | `bool` | 是否计算直接光照，默认 true |
| `mUseImportanceSampling` | `bool` | 是否使用材质重要性采样，默认 true |
| `mFrameCount` | `uint` | 自场景加载以来的帧计数 |
| `mTracer` | 匿名结构体 | 包含光线追踪程序 `pProgram`、绑定表 `pBindingTable` 和程序变量 `pVars` |

## 依赖关系

### 本文件引用

- `Falcor.h` — Falcor 核心头文件
- `RenderGraph/RenderPass.h` — 渲染通道基类
- `RenderGraph/RenderPassHelpers.h` — 渲染通道辅助工具
- `RenderGraph/RenderPassStandardFlags.h` — 标准刷新标志
- `Utils/Sampling/SampleGenerator.h` — 采样生成器

### 被以下文件引用

- `MinimalPathTracer.rt.slang` — 对应的光线追踪着色器

## 实现细节

- **着色器绑定表 (SBT)**: 支持三角形网格、位移三角形网格、曲线和 SDF 网格四种几何类型，每种类型设置散射光线和阴影光线的命中组。
- **宏定义传递**: 通过 `addDefine` 将 `MAX_BOUNCES`、`COMPUTE_DIRECT`、`USE_IMPORTANCE_SAMPLING` 等配置传递给着色器。
- **光源支持**: 支持解析光源、发光几何体和环境贴图三种光源类型，每种可独立启用/禁用。
- **景深支持**: 当相机有光圈半径时启用景深，但需要 `viewW` 输入缓冲区。
- **刷新标志**: 选项变更时通过 `RenderPassRefreshFlags::RenderOptionsChanged` 通知其他通道重置时间数据。
