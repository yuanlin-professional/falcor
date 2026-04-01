# ShaderToy 源码文档

> 路径: `Source/Samples/ShaderToy/ShaderToy.h` + `Source/Samples/ShaderToy/ShaderToy.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Samples/ShaderToy

## 功能概述

ShaderToy 示例演示了如何在 Falcor 中使用全屏着色器通道(FullScreenPass)实现类似 ShaderToy 风格的程序化渲染效果。程序将分辨率和全局时间作为参数传递给像素着色器,着色器负责生成所有视觉效果。

## 类与接口

### `ShaderToy`

- **继承**: `SampleApp`
- **职责**: 创建全屏渲染通道和渲染状态,每帧更新时间和分辨率参数并执行着色器

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `ShaderToy(const SampleAppConfig& config)` | 构造函数 |
| `~ShaderToy()` | 析构函数 |
| `void onLoad(RenderContext* pRenderContext) override` | 创建光栅化状态(无裁剪、无深度测试、不透明混合)、线性纹理采样器和全屏着色器通道 |
| `void onResize(uint32_t width, uint32_t height) override` | 更新宽高比 |
| `void onFrameRender(RenderContext* pRenderContext, const ref<Fbo>& pTargetFbo) override` | 设置分辨率和时间常量,执行全屏着色器 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpLinearSampler` | `ref<Sampler>` | 线性纹理采样器(最大各向异性 8) |
| `mAspectRatio` | `float` | 当前窗口宽高比 |
| `mpNoCullRastState` | `ref<RasterizerState>` | 无裁剪的光栅化状态 |
| `mpNoDepthDS` | `ref<DepthStencilState>` | 禁用深度测试的状态 |
| `mpOpaqueBS` | `ref<BlendState>` | 不透明混合状态 |
| `mpMainPass` | `ref<FullScreenPass>` | 全屏着色器渲染通道 |

## 依赖关系

### 本文件引用

- `Falcor.h` — Falcor 核心框架
- `Core/SampleApp.h` — 示例应用基类
- `Core/Pass/FullScreenPass.h` — 全屏渲染通道
- `Toy.ps.slang` — 程序化渲染像素着色器

### 被以下文件引用

- 无(顶层示例入口)

## 实现细节

- 使用 `FullScreenPass` 执行全屏四边形渲染,着色器接收 `iResolution`(分辨率)和 `iGlobalTime`(全局时间)参数。
- 这些参数命名模仿了 ShaderToy 的惯例,方便移植 ShaderToy 着色器代码。
- 窗口默认大小 1280x720,启用垂直同步。
- 光栅化状态和混合状态在 `onLoad` 中创建但未显式绑定到通道(FullScreenPass 使用默认状态)。
