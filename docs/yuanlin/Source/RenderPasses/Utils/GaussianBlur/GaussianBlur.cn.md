# GaussianBlur 源码文档

> 路径: `Source/RenderPasses/Utils/GaussianBlur/GaussianBlur.h` + `GaussianBlur.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/Utils/GaussianBlur

## 功能概述

GaussianBlur 实现了可分离的两遍高斯模糊渲染通道。先执行水平方向模糊，再执行垂直方向模糊，通过可配置的核宽度和 sigma 参数控制模糊程度。支持 2D 纹理和 2D 纹理数组。

## 类与接口

### `GaussianBlur`

- **继承**: `RenderPass`
- **职责**: 执行可分离的高斯模糊

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `GaussianBlur(ref<Device>, const Properties&)` | 构造函数，创建帧缓冲区和线性 Clamp 采样器 |
| `Properties getProperties() const` | 序列化核宽度和 sigma |
| `RenderPassReflection reflect(const CompileData&)` | 声明输入 `src` 和输出 `dst`，使用连接资源的格式 |
| `void compile(RenderContext*, const CompileData&)` | 创建水平/垂直模糊着色器，更新核权重 |
| `void execute(RenderContext*, const RenderData&)` | 依次执行水平模糊和垂直模糊 |
| `void renderUI(Gui::Widgets&)` | 核宽度和 sigma 滑块 |
| `void setKernelWidth(uint32_t)` | 设置核宽度（强制为奇数） |
| `void setSigma(float)` | 设置 sigma 值 |
| `static void registerBindings(pybind11::module&)` | 注册 Python 绑定 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mKernelWidth` | `uint32_t` | 核宽度（奇数，默认 5） |
| `mSigma` | `float` | 高斯标准差（默认 2.0） |
| `mpHorizontalBlur` / `mpVerticalBlur` | `ref<FullScreenPass>` | 水平/垂直模糊全屏着色器 |
| `mpFbo` / `mpTmpFbo` | `ref<Fbo>` | 输出和中间帧缓冲区 |
| `mpSampler` | `ref<Sampler>` | 线性 Clamp 采样器 |

## 依赖关系

### 本文件引用

- `Falcor.h`
- `Core/Pass/FullScreenPass.h`
- `RenderGraph/RenderPass.h`
- `GaussianBlur.ps.slang`（着色器）

### 被以下文件引用

- `Utils.cpp`（插件注册）

## 实现细节

1. **可分离实现**: 将 2D 高斯核分解为两个 1D 核，分别沿水平和垂直方向执行，将复杂度从 O(n^2) 降低到 O(2n)。
2. **权重计算**: `updateKernel()` 使用高斯函数 `e^(-x^2 / 2*sigma^2) / (2*pi*sigma^2)` 计算权重并归一化。
3. **共享变量**: 水平和垂直两个着色器通道共享相同的 `Vars`（通过 `setVars`），包括权重缓冲区。
4. **纹理数组支持**: 当输入为纹理数组时，使用 `_USE_TEX2D_ARRAY` 宏切换采样方式，并通过 `layerMask` 控制渲染目标数组层。
5. **核宽度强制奇数**: `setKernelWidth` 通过 `| 1` 确保核宽度始终为奇数。
