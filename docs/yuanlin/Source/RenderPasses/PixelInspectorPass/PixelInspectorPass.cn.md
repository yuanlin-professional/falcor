# PixelInspectorPass 源码文档

> 路径: `Source/RenderPasses/PixelInspectorPass/PixelInspectorPass.h` + `PixelInspectorPass.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/PixelInspectorPass

## 功能概述

像素检查器渲染通道，用于在选中的像素位置提取并显示详细的几何属性、材质属性和输出数据。支持鼠标点击选择像素和连续追踪模式，可查看世界位置、法线、切线、纹理坐标、材质 ID、BSDF 属性、线性/输出颜色、亮度以及可见性缓冲区数据。

## 类与接口

### `PixelInspectorPass`

- **继承**: `RenderPass`
- **职责**: 在指定像素位置运行计算着色器，提取几何和材质信息并在 UI 中显示

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `PixelInspectorPass(ref<Device>, const Properties&)` | 构造函数 |
| `RenderPassReflection reflect(const CompileData&)` | 声明 10 个可选输入通道（posW、normW、tangentW、faceNormalW、texC、texGrads、mtlData、linColor、outColor、vbuffer） |
| `void execute(RenderContext*, const RenderData&)` | 执行计算着色器提取像素数据 |
| `void renderUI(Gui::Widgets&)` | 显示像素信息（输出数据、几何数据、材质数据、可见性数据） |
| `void setScene(RenderContext*, const ref<Scene>&)` | 设置场景 |
| `bool onMouseEvent(const MouseEvent&)` | 处理鼠标事件更新光标位置 |
| `void recreatePrograms()` | 重建着色器程序（场景变更时） |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpScene` | `ref<Scene>` | 当前场景 |
| `mpPixelDataBuffer` | `ref<Buffer>` | 像素数据结构化缓冲区 |
| `mSelectedPixel` | `uint2` | 选中的像素坐标 |
| `mScaleInputsToWindow` | `bool` | 是否将输入缩放到窗口大小 |
| `mUseContinuousPicking` | `bool` | 是否连续追踪鼠标下的像素 |
| `mAvailableInputs` | `unordered_map<string, bool>` | 各输入通道可用性 |

## 依赖关系

### 本文件引用

- `Falcor.h`、`RenderGraph/RenderPass.h`、`RenderGraph/RenderPassHelpers.h`
- `PixelInspectorData.slang` — 像素数据结构体定义
- `PixelInspector.cs.slang` — 计算着色器

### 被以下文件引用

- 渲染图系统通过插件注册机制加载

## 实现细节

- 使用 `addRenderPassInputs` 批量声明 10 个输入通道
- 输入纹理分辨率可能与输出不同，支持坐标缩放（`mScaleInputsToWindow`）
- 计算着色器以 1x1x1 调度，仅处理选中的单个像素
- UI 分为四个可折叠组：输出数据（颜色、亮度）、几何数据（位置、法线、切线等）、材质数据（BSDF 属性）、可见性数据（VBuffer 解码）
