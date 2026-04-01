# SimplePostFX 源码文档

> 路径: `Source/RenderPasses/SimplePostFX/SimplePostFX.h` + `SimplePostFX.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/SimplePostFX

## 功能概述

SimplePostFX 是一组简单的后处理特效渲染通道。它对输入图像逐像素应用多种效果，包括：泛光（Bloom）、六角星光、暗角（Vignette）、色差（Chromatic Aberration）、桶形畸变（Barrel Distortion）、亮度相关的饱和度调整、以及 ASC-CDL 风格的偏移/缩放/幂次色彩校正。

泛光效果通过构建图像金字塔实现（8 级下采样，使用 10x10 二项式核），然后从粗到细逐级上采样，在每一级混合一小部分粗层到当前层，从而形成多尺度高斯叠加的最终核。

## 类与接口

### `SimplePostFX`

- **继承**: `RenderPass`
- **职责**: 实现一组可配置的后处理图像特效

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `SimplePostFX(ref<Device>, const Properties&)` | 构造函数，解析属性字典，创建采样器和三个计算通道 |
| `Properties getProperties() const` | 序列化当前所有参数为属性字典 |
| `RenderPassReflection reflect(const CompileData&)` | 声明输入 `src` 和输出 `dst` 纹理资源 |
| `void execute(RenderContext*, const RenderData&)` | 主执行逻辑：若参数为默认值则直接拷贝；否则执行下采样、上采样、后处理三阶段 |
| `void renderUI(Gui::Widgets&)` | 渲染 UI 控件，包括输出尺寸、镜头特效、饱和度、偏移/幂次/缩放分组 |
| `void preparePostFX(RenderContext*, uint32_t, uint32_t)` | 准备/重新分配图像金字塔纹理 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mEnabled` | `bool` | 是否启用整个渲染通道 |
| `mWipe` | `float` | 擦除比例，0 = 全效果，1 = 无效果 |
| `mBloomAmount` | `float` | 泛光强度 |
| `mStarAmount` | `float` | 六角星光强度 |
| `mStarAngle` | `float` | 星光角度 |
| `mVignetteAmount` | `float` | 暗角强度 |
| `mChromaticAberrationAmount` | `float` | 径向色差强度 |
| `mBarrelDistortAmount` | `float` | 桶形畸变强度 |
| `mSaturationCurve` | `float3` | 阴影/中间调/高光的饱和度值 |
| `mColorOffset` / `mColorScale` / `mColorPower` | `float3` | 色彩偏移/缩放/幂次（RGB 通道） |
| `mColorOffsetScalar` / `mColorScaleScalar` / `mColorPowerScalar` | `float` | 亮度偏移/缩放/幂次标量 |
| `mpPyramid[kNumLevels+1]` | `ref<Texture>[]` | 图像金字塔纹理数组（8+1 级） |
| `mpDownsamplePass` / `mpUpsamplePass` / `mpPostFXPass` | `ref<ComputePass>` | 下采样/上采样/后处理计算通道 |
| `mpLinearSampler` | `ref<Sampler>` | 线性采样器，Border 寻址模式 |
| `mOutputSizeSelection` | `RenderPassHelpers::IOSize` | 输出尺寸选择 |

## 依赖关系

### 本文件引用

- `Falcor.h`
- `RenderGraph/RenderPass.h`
- `RenderGraph/RenderPassHelpers.h`
- `SimplePostFX.cs.slang`（着色器文件）
- `Utils/Color/ColorHelpers.slang`（间接，通过着色器）

### 被以下文件引用

- 作为独立插件通过 `registerPlugin` 注册到 Falcor 插件系统

## 实现细节

1. **属性序列化/反序列化**: 构造函数和 `getProperties()` 通过键值对实现完整的属性持久化，支持 Python 脚本绑定。
2. **优化快速路径**: `execute()` 中检测到所有参数为默认值时，直接使用 `blit` 拷贝，避免不必要的 GPU 工作。
3. **图像金字塔**: 使用 8 级下采样（每级 4x 缩小），每级使用 5x5 双线性采样核（有效覆盖 10x10），确保抗锯齿。
4. **星光效果**: 在上采样的第 1、2 级通过沿 120 度间隔的三个方向进行暴力线采样实现。
5. **Python 绑定**: 通过 `regSimplePostFX` 注册所有参数为 pybind11 属性，支持脚本化控制。
