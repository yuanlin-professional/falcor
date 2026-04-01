# SampleAppTemplate 源码文档

> 路径: `Source/Samples/SampleAppTemplate/SampleAppTemplate.h` + `Source/Samples/SampleAppTemplate/SampleAppTemplate.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Samples/SampleAppTemplate

## 功能概述

SampleAppTemplate 是 Falcor 框架的示例应用模板,提供了一个最小化的应用骨架。该模板覆盖了 `SampleApp` 基类的所有主要回调函数,但大部分实现为空,仅在 `onFrameRender` 中清除帧缓冲区为绿色,在 `onGuiRender` 中显示一个简单的 GUI 窗口。开发者可以基于此模板快速创建新的 Falcor 示例应用。

## 类与接口

### `SampleAppTemplate`

- **继承**: `SampleApp`
- **职责**: 作为新示例应用的起始模板,提供所有生命周期回调的空实现

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `SampleAppTemplate(const SampleAppConfig& config)` | 构造函数 |
| `~SampleAppTemplate()` | 析构函数 |
| `void onLoad(RenderContext* pRenderContext) override` | 资源加载回调(空实现) |
| `void onShutdown() override` | 关闭回调(空实现) |
| `void onResize(uint32_t width, uint32_t height) override` | 窗口大小改变回调(空实现) |
| `void onFrameRender(RenderContext* pRenderContext, const ref<Fbo>& pTargetFbo) override` | 每帧渲染,清除帧缓冲区为绿色 |
| `void onGuiRender(Gui* pGui) override` | GUI 渲染,显示文本和按钮 |
| `bool onKeyEvent(const KeyboardEvent& keyEvent) override` | 键盘事件(返回 false) |
| `bool onMouseEvent(const MouseEvent& mouseEvent) override` | 鼠标事件(返回 false) |
| `void onHotReload(HotReloadFlags reloaded) override` | 热重载回调(空实现) |

#### 关键成员

无私有成员变量。

## 依赖关系

### 本文件引用

- `Falcor.h` — Falcor 核心框架
- `Core/SampleApp.h` — 示例应用基类

### 被以下文件引用

- 无(顶层示例入口)

## 实现细节

- `onFrameRender` 使用 `clearFbo` 将目标帧缓冲区清除为 `(0.38, 0.52, 0.10, 1)` 绿色。
- `onGuiRender` 创建一个 250x200 的 GUI 窗口,显示 "Hello from SampleAppTemplate" 文本和一个按钮,点击按钮弹出消息框。
- 全局变量 `mSampleGuiWidth/Height/PositionX/Y` 定义了 GUI 的默认尺寸和位置(在 cpp 中定义但未使用)。
- 此模板覆盖了 `SampleApp` 的所有虚函数,方便开发者了解可用的回调接口。
