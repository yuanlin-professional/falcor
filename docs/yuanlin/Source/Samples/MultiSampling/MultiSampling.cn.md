# MultiSampling 源码文档

> 路径: `Source/Samples/MultiSampling/MultiSampling.h` + `Source/Samples/MultiSampling/MultiSampling.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Samples/MultiSampling

## 功能概述

MultiSampling 示例演示了如何在 Falcor 中使用多重采样抗锯齿 (MSAA)。程序生成一个由 16 个三角形组成的圆盘几何体,使用 8x MSAA 渲染到 128x128 的多重采样帧缓冲区,然后交替使用两种方式将结果输出到屏幕:偶数帧先解析(resolve)再 blit,奇数帧直接 blit 多重采样纹理。

## 类与接口

### `MultiSampling`

- **继承**: `SampleApp`
- **职责**: 创建圆盘几何体、多重采样帧缓冲区,并使用 MSAA 渲染后输出到屏幕

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `MultiSampling(const SampleAppConfig& config)` | 构造函数 |
| `~MultiSampling()` | 析构函数 |
| `void onLoad(RenderContext* pRenderContext) override` | 创建着色器程序、圆盘顶点缓冲区、顶点布局、VAO、多重采样 FBO 和解析纹理 |
| `void onFrameRender(RenderContext* pRenderContext, const ref<Fbo>& pTargetFbo) override` | 清除 FBO、执行光栅化绘制、交替使用 resolve+blit 或直接 blit |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpRasterPass` | `ref<RasterPass>` | 光栅化渲染通道 |
| `mpVao` | `ref<Vao>` | 顶点数组对象(圆盘几何体) |
| `mpFbo` | `ref<Fbo>` | 多重采样帧缓冲区(128x128, 8x MSAA, RGBA32Float) |
| `mpResolvedTexture` | `ref<Texture>` | 解析后的非多重采样纹理(128x128, RGBA32Float) |
| `mFrame` | `uint32_t` | 帧计数器,用于交替选择输出方式 |

## 依赖关系

### 本文件引用

- `Falcor.h` — Falcor 核心框架
- `Core/SampleApp.h` — 示例应用基类
- `Core/Pass/RasterPass.h` — 光栅化渲染通道
- `MultiSampling.3d.slang` — 顶点/像素着色器

### 被以下文件引用

- 无(顶层示例入口)

## 实现细节

- 圆盘几何体由 16 个三角形构成,顶点围绕圆心均匀分布在 0.75 半径的圆上。
- 顶点布局仅包含 `POSITION` 属性,格式为 `RG32Float`(2D 坐标)。
- 多重采样纹理通过 `createTexture2DMS` 创建,采样数为 8。
- 偶数帧使用 `resolveResource` 将多重采样纹理解析为单采样纹理再 blit;奇数帧直接从多重采样纹理 blit(由 GPU 硬件处理解析)。
- 窗口默认大小 1024x1024,启用垂直同步。
