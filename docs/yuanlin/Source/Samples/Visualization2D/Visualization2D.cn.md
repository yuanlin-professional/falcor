# Visualization2D 源码文档

> 路径: `Source/Samples/Visualization2D/Visualization2D.h` + `Source/Samples/Visualization2D/Visualization2D.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Samples/Visualization2D

## 功能概述

Visualization2D 示例提供了两种 2D 可视化场景的演示:标记演示(Marker Demo)和体素法线可视化(Voxel Normals)。该示例展示了如何使用全屏渲染通道(FullScreenPass)和 SDF(有符号距离场)2D 图元进行程序化 2D 图形渲染,支持鼠标交互和实时参数调节。

## 类与接口

### `VoxelNormalsGUI`

- **职责**: 存储体素法线可视化场景的 GUI 控制参数

| 成员 | 类型 | 说明 |
|------|------|------|
| `showNormalField` | `bool` | 是否显示法线场(默认 false) |
| `showBoxes` | `bool` | 是否显示体素盒子(默认 true) |
| `showBoxDiagonals` | `bool` | 是否显示盒子对角线(默认 true) |
| `showBorderLines` | `bool` | 是否显示边界线(默认 false) |
| `showBoxAroundPoint` | `bool` | 是否显示鼠标点周围的盒子(默认 false) |

### `Visualization2D`

- **继承**: `SampleApp`
- **职责**: 管理 2D 可视化场景的切换、GUI 控制和全屏着色器的执行

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `Visualization2D(const SampleAppConfig& config)` | 构造函数 |
| `~Visualization2D()` | 析构函数 |
| `void onLoad(RenderContext* pRenderContext) override` | 创建默认渲染通道 |
| `void onFrameRender(RenderContext* pRenderContext, const ref<Fbo>& pTargetFbo) override` | 设置着色器参数(分辨率、时间、鼠标位置、体素法线GUI参数)并执行渲染 |
| `void onGuiRender(Gui* pGui) override` | 渲染场景选择下拉框和各场景的专属控件 |
| `bool onKeyEvent(const KeyboardEvent& keyEvent) override` | 键盘事件(返回 false) |
| `bool onMouseEvent(const MouseEvent& mouseEvent) override` | 追踪鼠标左键按下状态和位置 |
| `void createRenderPass()` | 根据当前选中的场景创建对应的全屏着色器通道 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpMainPass` | `ref<FullScreenPass>` | 全屏着色器渲染通道 |
| `mLeftButtonDown` | `bool` | 鼠标左键是否按下 |
| `mMousePosition` | `float2` | 鼠标位置(屏幕坐标) |
| `mVoxelNormalsGUI` | `VoxelNormalsGUI` | 体素法线场景的 GUI 参数 |
| `mSelectedScene` | `Scene` | 当前选中的场景(MarkerDemo 或 VoxelNormals) |

#### 枚举类型

### `Visualization2D::Scene`

| 枚举值 | 说明 |
|--------|------|
| `MarkerDemo` | 标记演示场景 |
| `VoxelNormals` | 体素法线可视化场景 |

## 依赖关系

### 本文件引用

- `Falcor.h` — Falcor 核心框架
- `Core/SampleApp.h` — 示例应用基类
- `Core/Pass/FullScreenPass.h` — 全屏渲染通道
- `Visualization2d.ps.slang` — 标记演示着色器
- `VoxelNormals.ps.slang` — 体素法线着色器

### 被以下文件引用

- 无(顶层示例入口)

## 实现细节

- 两个场景使用不同的着色器文件,通过 `createRenderPass` 方法在切换时重新创建 `FullScreenPass`。
- 两个着色器共享相同的 `Visual2DCB` 常量缓冲区(分辨率、时间、鼠标位置)。
- 体素法线场景额外使用 `VoxelNormalsCB` 常量缓冲区传递 GUI 控制参数。
- 鼠标交互:仅在左键按下时更新鼠标位置,用于着色器中的交互式可视化。
- 窗口默认大小 1400x1000,启用垂直同步。
