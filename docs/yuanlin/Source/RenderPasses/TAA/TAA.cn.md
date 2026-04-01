# TAA 源码文档

> 路径: `Source/RenderPasses/TAA/TAA.h` + `TAA.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/TAA

## 功能概述

本文件实现了时序抗锯齿（Temporal Anti-Aliasing, TAA）渲染通道。该通道通过将当前帧颜色与前一帧的历史颜色进行加权混合来实现抗锯齿效果，使用运动向量进行历史采样的重投影，并通过颜色包围盒裁剪来减少鬼影。

## 类与接口

### `TAA`

- **继承**: `RenderPass`
- **职责**: 执行时序抗锯齿处理

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `TAA(ref<Device>, const Properties&)` | 构造函数，创建全屏通道、FBO 和线性采样器，解析属性 |
| `Properties getProperties() const` | 序列化属性（alpha、colorBoxSigma、antiFlicker） |
| `RenderPassReflection reflect(const CompileData&)` | 声明输入（motionVecs、colorIn）和输出（colorOut） |
| `void execute(RenderContext*, const RenderData&)` | 执行 TAA：分配前帧缓冲区 -> 设置参数 -> 运行着色器 -> 复制输出到前帧缓冲区 |
| `void renderUI(Gui::Widgets&)` | 渲染 UI：Alpha 滑块、Color-Box Sigma 滑块、Anti Flicker 开关 |
| `void allocatePrevColor(const Texture*)` | 按需分配/重建前帧颜色纹理 |

#### Getter/Setter

| 方法 | 说明 |
|------|------|
| `setAlpha(float)` / `getAlpha()` | 混合权重（当前帧与历史帧） |
| `setColorBoxSigma(float)` / `getColorBoxSigma()` | 颜色包围盒的 sigma 系数 |
| `setAntiFlicker(bool)` / `getAntiFlicker()` | 抗闪烁开关 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpPass` | `ref<FullScreenPass>` | TAA 全屏通道 |
| `mpFbo` | `ref<Fbo>` | 帧缓冲区对象 |
| `mpLinearSampler` | `ref<Sampler>` | 线性过滤采样器 |
| `mControls.alpha` | `float` | 混合权重，默认 0.1（更小 = 更多历史帧权重） |
| `mControls.colorBoxSigma` | `float` | 颜色包围盒大小系数，默认 1.0 |
| `mControls.antiFlicker` | `bool` | 抗闪烁开关，默认 true |
| `mpPrevColor` | `ref<Texture>` | 前一帧颜色纹理 |

## 依赖关系

### 本文件引用

- `Falcor.h` — 核心框架
- `Core/Pass/FullScreenPass.h` — 全屏通道
- `RenderGraph/RenderPass.h` — 渲染通道基类
- 着色器: `TAA.ps.slang`

### 被以下文件引用

- 渲染图配置脚本通过 `TAA` 名称引用

## 实现细节

1. **历史缓冲区管理**: `allocatePrevColor` 在输出纹理尺寸、深度或格式变化时重建历史颜色纹理。每帧结束后将输出 blit 到历史纹理供下一帧使用。

2. **尺寸验证**: 运行时断言确保颜色输入、前帧颜色和运动向量纹理的尺寸完全一致，且均为单采样。

3. **Python 脚本接口**: 通过 pybind11 暴露 `alpha`、`sigma`（映射到 colorBoxSigma）、`antiFlicker` 属性。
