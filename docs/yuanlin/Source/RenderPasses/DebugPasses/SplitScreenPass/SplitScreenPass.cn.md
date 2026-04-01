# SplitScreenPass 源码文档

> 路径: `Source/RenderPasses/DebugPasses/SplitScreenPass/SplitScreenPass.h` + `SplitScreenPass.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/DebugPasses/SplitScreenPass

## 功能概述

SplitScreenPass 允许用户通过可交互的分割线在两个输入图像之间进行分屏对比。支持鼠标拖动分割线、双击重置到中间、悬停时显示箭头指示器等交互功能。

## 类与接口

### `SplitScreenPass`

- **继承**: `ComparisonPass`
- **职责**: 实现交互式分屏图像对比

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `SplitScreenPass(ref<Device>, const Properties&)` | 构造函数，创建 16x16 箭头纹理和分屏着色器 |
| `void createProgram()` | 创建全屏分屏着色器 |
| `void execute(RenderContext*, const RenderData&)` | 设置分割线颜色、鼠标位置、箭头参数后调用基类执行 |
| `bool onMouseEvent(const MouseEvent&)` | 处理鼠标事件：悬停高亮、拖动分割线、双击重置 |
| `void renderUI(Gui::Widgets&)` | 分割位置滑块、显示箭头复选框 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpArrowTex` | `ref<Texture>` | 16x16 灰度箭头纹理 |
| `mMouseOverDivider` | `bool` | 鼠标是否悬停在分割线上 |
| `mMousePos` | `int2` | 鼠标当前位置 |
| `mDividerGrabbed` | `bool` | 是否正在拖动分割线 |
| `mDrawArrows` | `bool` | 悬停时是否显示箭头 |
| `mTimeOfLastClick` | `CpuTimer::TimePoint` | 上次鼠标点击时间（用于双击检测） |

## 依赖关系

### 本文件引用

- `ComparisonPass.h`（基类）
- `Utils/Timing/CpuTimer.h`
- `SplitScreen.ps.slang`（着色器）

### 被以下文件引用

- `DebugPasses.cpp`（插件注册）

## 实现细节

1. **箭头纹理**: 在构造函数中从硬编码的 256 字节灰度数组创建 16x16 的 R8Unorm 纹理。
2. **双击重置**: 使用 `CpuTimer` 检测 100ms 内的双击事件，将分割线重置到 0.5（屏幕中间）。
3. **悬停检测**: 鼠标与分割线距离小于 `max(6, mDividerSize)` 像素时判定为悬停，确保最小 13 像素的交互区域。
