# SideBySidePass 源码文档

> 路径: `Source/RenderPasses/DebugPasses/SideBySidePass/SideBySidePass.h` + `SideBySidePass.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/DebugPasses/SideBySidePass

## 功能概述

SideBySidePass 允许用户将两个输入图像并排显示进行对比。与 SplitScreenPass 不同，此通道将两幅图像分别显示在分割线左右两侧（各自的图像内容可以通过偏移来控制），而不是在同一位置切换显示。

## 类与接口

### `SideBySidePass`

- **继承**: `ComparisonPass`
- **职责**: 实现并排图像对比

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `SideBySidePass(ref<Device>, const Properties&)` | 构造函数，创建着色器并解析属性 |
| `void createProgram()` | 创建全屏并排着色器 |
| `void execute(RenderContext*, const RenderData&)` | 设置左边界参数后调用基类执行 |
| `void renderUI(Gui::Widgets&)` | 视图滑块控制左边界偏移 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mImageLeftBound` | `uint32_t` | 输出左侧在原始输入图像中的起始位置（像素） |

## 依赖关系

### 本文件引用

- `ComparisonPass.h`（基类）
- `SideBySide.ps.slang`（着色器）

### 被以下文件引用

- `DebugPasses.cpp`（插件注册）
