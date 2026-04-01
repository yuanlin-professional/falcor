# LightProfile 源码文档

> 路径: `Source/Falcor/Scene/Lights/LightProfile.h`, `Source/Falcor/Scene/Lights/LightProfile.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Lights

## 功能概述

光源配置文件（Light Profile）类，用于加载和管理 IES 光源配置文件。IES 文件定义了光源在不同角度的光强分布，常用于建筑照明模拟。该类支持解析 IES 文件、通过计算着色器将光强数据烘焙到 2D 纹理中，并计算通量因子。

## 类与接口

### `LightProfile`

- **继承**: `Object`
- **职责**: 加载 IES 配置文件，烘焙为 GPU 纹理，提供着色器绑定和 UI 渲染。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<LightProfile> createFromIesProfile(ref<Device>, const path&, bool normalize)` | 从 IES 文件创建光源配置 |
| `void bake(RenderContext* pRenderContext)` | 将 IES 数据烘焙到 GPU 纹理（256x256，R16Float） |
| `void bindShaderData(const ShaderVar& var) const` | 绑定纹理和通量因子到着色器 |
| `void renderUI(Gui::Widgets& widget) const` | 渲染 UI（显示纹理预览和通量因子） |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mName` | `std::string` | 配置文件名称 |
| `mRawData` | `vector<float>` | 解析后的 IES 原始数值数据 |
| `mpTexture` | `ref<Texture>` | 烘焙后的 2D 纹理（R16Float） |
| `mpSampler` | `ref<Sampler>` | 线性过滤采样器 |
| `mFluxFactor` | `float` | 通量因子（配置文件在球面上的积分） |

## 依赖关系

### 本文件引用

- `Core/API/Texture.h`, `Core/API/Sampler.h` — 纹理和采样器
- `Core/Pass/ComputePass.h` — 计算着色器通道
- `Utils/Algorithm/ParallelReduction.h` — 并行归约（用于通量计算）
- `BakeIesProfile.cs.slang` — IES 烘焙计算着色器

### 被以下文件引用

- `Scene/Scene.h` — 场景光源管理
- 着色器端的 `LightProfile.slang`

## 实现细节

- 支持多种 IES 格式版本：IESNA:LM-63-1986/1991/1995/2002 及 ERCO 格式。
- 仅支持 `TILT=NONE` 的 IES 文件。
- 烘焙过程使用 `BakeIesProfile.cs.slang` 计算着色器，线程组大小为 16x16。
- 通量因子通过 `ParallelReduction` 对通量纹理求和计算得出。
- 归一化选项：当 `normalize=true` 时，最大坎德拉值归一化为 1.0。
