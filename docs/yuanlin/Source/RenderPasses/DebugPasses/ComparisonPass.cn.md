# ComparisonPass 源码文档

> 路径: `Source/RenderPasses/DebugPasses/ComparisonPass.h` + `ComparisonPass.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/DebugPasses

## 功能概述

ComparisonPass 是一个抽象基类，为所有图像对比渲染通道提供公共功能。它实现了左右两幅图像的分屏对比显示，支持可配置的分割位置、分割线宽度、交换左右侧、以及文字标签。

子类需要实现 `createProgram()` 来创建特定的分屏着色器程序。

## 类与接口

### `ComparisonPass`（抽象类）

- **继承**: `RenderPass`
- **职责**: 图像对比渲染通道的基类，提供分屏显示、文字标签和公共 UI

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `ComparisonPass(ref<Device>)` | 受保护的构造函数，创建文字渲染器 |
| `virtual void createProgram() = 0` | 纯虚函数，子类实现以创建分屏着色器 |
| `bool parseKeyValuePair(string, ConstValue)` | 解析公共属性（分割位置、标签等） |
| `Properties getProperties() const` | 序列化公共属性 |
| `RenderPassReflection reflect(const CompileData&)` | 声明 `leftInput`、`rightInput` 输入和 `output` 输出 |
| `void execute(RenderContext*, const RenderData&)` | 设置着色器参数并执行分屏渲染，可选绘制文字标签 |
| `void renderUI(Gui::Widgets&)` | 提供交换两侧和显示标签的复选框 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpSplitShader` | `ref<FullScreenPass>` | 分屏全屏着色器通道 |
| `pLeftSrcTex` / `pRightSrcTex` | `ref<Texture>` | 左/右输入纹理 |
| `pDstFbo` | `ref<Fbo>` | 目标帧缓冲区 |
| `mpTextRenderer` | `unique_ptr<TextRenderer>` | 文字渲染器 |
| `mSwapSides` | `bool` | 是否交换左右输入 |
| `mSplitLoc` | `float` | 分割线位置（屏幕宽度的比例，默认 0.5） |
| `mDividerSize` | `uint32_t` | 分割线半宽（像素） |
| `mShowLabels` | `bool` | 是否显示文字标签 |
| `mLeftLabel` / `mRightLabel` | `string` | 左/右标签文字 |

## 依赖关系

### 本文件引用

- `Falcor.h`
- `Core/Pass/FullScreenPass.h`
- `RenderGraph/RenderPass.h`
- `Utils/UI/TextRenderer.h`

### 被以下文件引用

- `SplitScreenPass/SplitScreenPass.h`
- `SideBySidePass/SideBySidePass.h`
