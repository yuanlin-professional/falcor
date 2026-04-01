# SampleAppTemplate -- 示例应用模板

## 功能概述

本项目是 Falcor 框架的**空白应用模板**，为开发者提供创建新示例程序的起点。它继承 `SampleApp` 基类，预留了所有关键虚函数的空实现，包括：

- `onLoad` -- 初始化资源
- `onShutdown` -- 清理资源
- `onResize` -- 响应窗口大小变化
- `onFrameRender` -- 每帧渲染（当前仅清屏为绿色）
- `onGuiRender` -- GUI 界面绘制（当前显示 "Hello from SampleAppTemplate" 和一个示例按钮）
- `onKeyEvent` / `onMouseEvent` -- 输入事件处理
- `onHotReload` -- 热重载回调

开发者可以在此模板基础上快速添加自己的渲染逻辑、着色器和 GUI 控件。

## 文件清单

| 文件名 | 类型 | 说明 |
|---|---|---|
| `SampleAppTemplate.cpp` | C++ 源文件 | 模板应用主逻辑，所有回调函数的骨架实现 |
| `SampleAppTemplate.h` | C++ 头文件 | `SampleAppTemplate` 类声明，继承 `SampleApp`，私有成员区为空 |
| `CMakeLists.txt` | 构建脚本 | CMake 配置 |

## 依赖关系

- `SampleAppTemplate.cpp` 包含 `SampleAppTemplate.h`。
- `SampleAppTemplate.h` 依赖 `Falcor.h` 和 `Core/SampleApp`。
- 本模板不包含任何着色器文件，也无外部资源依赖。
