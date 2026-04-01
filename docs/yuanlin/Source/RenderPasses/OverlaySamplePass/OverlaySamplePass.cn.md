# OverlaySamplePass 源码文档

> 路径: `Source/RenderPasses/OverlaySamplePass/OverlaySamplePass.h` + `OverlaySamplePass.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/OverlaySamplePass

## 功能概述

OverlaySamplePass 是一个演示用渲染通道，展示如何在渲染通道中使用 `renderOverlayUI` 回调来直接通过 ImGui 在屏幕上绘制简单几何图形。该通道将输入缓冲区直接拷贝到输出，然后在输出上叠加一个 5x3 的图元网格，展示 ImGui 支持的所有基本绘制图元。

## 类与接口

### `OverlaySamplePass`

- **继承**: `RenderPass`
- **职责**: 演示 Overlay UI 绘制功能，展示 ImGui 的各种基本图元

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `OverlaySamplePass(ref<Device>, const Properties&)` | 构造函数 |
| `Properties getProperties() const` | 返回空属性 |
| `RenderPassReflection reflect(const CompileData&)` | 声明输入 `input`（可选，RGBA32Float）和输出 `output`（RGBA32Float） |
| `void execute(RenderContext*, const RenderData&)` | 将输入拷贝到输出，更新帧尺寸和帧计数 |
| `void renderOverlayUI(RenderContext*)` | 使用 ImGui DrawList 绘制 15 种不同图元的展示网格 |
| `static void registerBindings(pybind11::module&)` | 注册 Python 绑定（空实现） |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mFrameDim` | `uint2` | 当前帧尺寸 |
| `mFrameCount` | `uint32_t` | 帧计数器 |

## 依赖关系

### 本文件引用

- `Falcor.h`
- `RenderGraph/RenderPass.h`
- `RenderGraph/RenderPassHelpers.h`
- `RenderGraph/RenderPassStandardFlags.h`
- `imgui.h` — 直接使用 ImGui 绘制列表

### 被以下文件引用

- 作为独立插件通过 `registerPlugin` 注册

## 实现细节

1. **图元展示**: `renderOverlayUI` 在一个带红色边框的矩形区域内创建 5x3 = 15 个子区域，每个区域绘制一种不同的 ImGui 图元：直线、矩形、多色填充矩形、菱形线框/填充、三角形线框/填充、圆形线框/填充、正多边形线框/填充、文字、折线星形、凸多边形填充、贝塞尔曲线。
2. **Overlay 机制**: `renderOverlayUI` 在 `renderUI` 之后触发，即使下拉菜单关闭时也会触发，使用 `ImGui::GetBackgroundDrawList()` 直接操作背景绘制列表。
3. **无着色器依赖**: 此通道不使用任何自定义着色器，仅通过 CPU 端 ImGui 调用完成所有绘制。
