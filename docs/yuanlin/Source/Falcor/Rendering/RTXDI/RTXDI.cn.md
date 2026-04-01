# RTXDI 源码文档

> 路径: `Source/Falcor/Rendering/RTXDI/RTXDI.h` + `Source/Falcor/Rendering/RTXDI/RTXDI.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Rendering/RTXDI

## 功能概述

RTXDI (RTX Direct Illumination) 是 NVIDIA 提供的实时直接光照重采样框架的封装模块。本文件实现了 RTXDI SDK 在 Falcor 渲染框架中的集成，提供了基于 ReSTIR (Reservoir-based Spatio-Temporal Importance Resampling) 的高效光源采样能力。该模块负责管理 RTXDI 所需的所有 GPU 资源（光源信息、表面数据缓冲区、内部缓冲区），并协调每帧的采样/重采样流程。

集成流程分为以下步骤：
1. `beginFrame()` — 帧开始初始化
2. 着色器中调用 `setSurfaceData()` 填充主命中点的表面数据
3. `update()` — 执行 RTXDI 重采样
4. 着色器中调用 `getFinalSample()` 获取最终光源样本
5. `endFrame()` — 帧结束清理

## 类与接口

### `RTXDI`

- **继承**: 无
- **职责**: RTXDI SDK 的主机端封装，管理光源数据更新、资源分配和 GPU 计算通道的调度

#### 枚举类型

| 枚举 | 说明 |
|------|------|
| `Mode` | 采样模式：`NoResampling`（无重采样）、`SpatialResampling`（空间重采样）、`TemporalResampling`（时间重采样）、`SpatiotemporalResampling`（时空重采样） |
| `BiasCorrection` | 偏差校正模式：`Off`（1/M 归一化）、`Basic`（基于 MIS 的归一化）、`Pairwise`（成对 MIS）、`RayTraced`（光线追踪可见性，无偏） |

#### 内部结构体 `Options`

| 成员 | 类型 | 说明 |
|------|------|------|
| `mode` | `Mode` | 采样模式，默认时空重采样 |
| `presampledTileCount` | `uint32_t` | 预采样光源瓦片数量（128） |
| `presampledTileSize` | `uint32_t` | 每瓦片采样数（1024） |
| `storeCompactLightInfo` | `bool` | 是否存储紧凑光源信息以提升内存一致性 |
| `localLightCandidateCount` | `uint32_t` | 局部光源初始候选数（24） |
| `infiniteLightCandidateCount` | `uint32_t` | 无限远光源初始候选数（8） |
| `envLightCandidateCount` | `uint32_t` | 环境光初始候选数（8） |
| `brdfCandidateCount` | `uint32_t` | BRDF 候选数（1） |
| `brdfCutoff` | `float` | BRDF 射线截断比例 |
| `testCandidateVisibility` | `bool` | 是否在重采样前测试候选样本可见性 |
| `biasCorrection` | `BiasCorrection` | 偏差校正模式 |
| `depthThreshold` | `float` | 深度差异阈值（10%） |
| `normalThreshold` | `float` | 法线夹角余弦阈值 |
| `samplingRadius` | `float` | 空间重采样的屏幕空间半径（像素） |
| `spatialSampleCount` | `uint32_t` | 空间重采样邻域采样数 |
| `spatialIterations` | `uint32_t` | 空间重采样迭代次数 |
| `maxHistoryLength` | `uint32_t` | 时间重用最大历史帧数 |
| `boilingFilterStrength` | `float` | 沸腾滤波器强度 |
| `rayEpsilon` | `float` | 可见性射线的自交避免偏移量 |
| `useEmissiveTextures` | `bool` | 最终着色时是否使用发光纹理（更慢但更精确） |
| `enableVisibilityShortcut` | `bool` | 跨帧重用可见性以降低开销 |
| `enablePermutationSampling` | `bool` | 启用像素排列采样（更利于降噪器） |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static bool isInstalled()` | 检查 RTXDI SDK 是否已安装 |
| `RTXDI(ref<IScene>, const Options&)` | 构造函数，初始化场景和选项，要求 SM 6.5 |
| `void setOptions(const Options&)` | 设置配置选项 |
| `const Options& getOptions()` | 获取当前配置选项 |
| `bool renderUI(Gui::Widgets&)` | 渲染 GUI 控件 |
| `DefineList getDefines() const` | 获取着色器预处理宏定义列表 |
| `void bindShaderData(const ShaderVar&)` | 绑定 RTXDI 数据到全局变量 `gRTXDI` |
| `void beginFrame(RenderContext*, const uint2&)` | 帧开始，更新光源、准备资源 |
| `void endFrame(RenderContext*)` | 帧结束，交换缓冲区索引 |
| `void update(RenderContext*, const ref<Texture>&)` | 执行完整的 RTXDI 重采样管线 |
| `PixelDebug& getPixelDebug()` | 获取像素调试组件 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpScene` | `ref<IScene>` | 场景引用 |
| `mpDevice` | `ref<Device>` | GPU 设备 |
| `mOptions` | `Options` | 配置选项 |
| `mpPixelDebug` | `unique_ptr<PixelDebug>` | 像素调试组件 |
| `mpRTXDIContext` | `unique_ptr<rtxdi::Context>` | RTXDI SDK 上下文 |
| `mLights` | 匿名结构体 | 光源映射信息（发光体、解析光源、环境光的数量和索引） |
| `mFlags` | 匿名结构体 | 更新标志（光源变化、着色器重编译、蓄水池清除等） |
| `mpReservoirBuffer` | `ref<Buffer>` | 光源蓄水池缓冲区 |
| `mpSurfaceDataBuffer` | `ref<Buffer>` | 当前帧和前帧的表面数据缓冲区 |
| `mpLightInfoBuffer` | `ref<Buffer>` | 场景所有光源信息缓冲区 |
| `mpLocalLightPdfTexture` | `ref<Texture>` | 局部光源 PDF 纹理 |
| `mpEnvLightLuminanceTexture` | `ref<Texture>` | 环境光亮度纹理 |
| `mpEnvLightPdfTexture` | `ref<Texture>` | 环境光 PDF 纹理 |

## 依赖关系

### 本文件引用

- `Core/Macros.h`, `Core/Enum.h`, `Core/Error.h`, `Core/API/RenderContext.h`
- `Utils/Properties.h`, `Utils/Logger.h`, `Utils/Math/Common.h`, `Utils/Timing/Profiler.h`
- `Utils/Debug/PixelDebug.h`
- `Scene/Scene.h`
- `rtxdi/RTXDI.h`（RTXDI SDK 头文件，条件编译）
- 着色器文件: `ReflectTypes.cs.slang`, `RTXDISetup.cs.slang`, `LightUpdater.cs.slang`, `EnvLightUpdater.cs.slang`

### 被以下文件引用

- `RTXDIPass` 渲染通道
- 路径追踪器中的直接光照采样

## 实现细节

- **光源分类映射**: Falcor 的发光三角形和局部解析光源映射为 RTXDI 的局部光源；方向光和远距光映射为无限远光源；环境贴图单独处理
- **双缓冲表面数据**: 使用 `currentSurfaceBufferIndex` 在两个表面数据缓冲区间切换，实现时间重采样的帧间数据传递
- **蓄水池管理**: 分配 3 个屏幕大小的蓄水池缓冲区，候选样本固定在蓄水池 #2，其余用于时空重采样的输入输出交替
- **计算通道调度**: `update()` 按顺序执行预采样 -> 生成候选 -> 可见性测试 -> 重采样（空间/时间/时空），根据 `Mode` 选择不同重采样路径
- **条件编译**: 当 `FALCOR_HAS_RTXDI` 未定义时，大部分实现被裁剪，各公开方法退化为空操作
