# SampleApp 源码文档

> 路径: `Source/Falcor/Core/SampleApp.h` + `Source/Falcor/Core/SampleApp.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core

## 功能概述

定义 Falcor 示例应用程序基类 `SampleApp`，提供完整的应用程序生命周期管理，包括窗口创建、GPU 设备初始化、交换链管理、渲染循环、UI 系统、输入处理、截图、脚本绑定等功能。开发者通过继承此类并重写虚函数来实现自定义渲染应用。

## 结构体

### `SampleAppConfig`
示例应用配置结构体。

| 成员 | 类型 | 说明 |
|------|------|------|
| `deviceDesc` | `Device::Desc` | GPU 设备配置 |
| `windowDesc` | `Window::Desc` | 窗口配置 |
| `colorFormat` | `ResourceFormat` | 帧缓冲颜色格式（默认 BGRA8UnormSrgb） |
| `depthFormat` | `ResourceFormat` | 深度缓冲区格式（默认 D32Float） |
| `headless` | `bool` | 无头模式（不创建窗口） |
| `timeScale` | `float` | 时间缩放因子 |
| `pauseTime` | `bool` | 启动时暂停时钟 |
| `showUI` | `bool` | 显示 UI |
| `generateShaderDebugInfo` | `bool` | 生成着色器调试信息 |
| `shaderPreciseFloat` | `bool` | 着色器精确浮点模式 |

## 类与接口

### `SampleApp`
- **继承**: `Window::ICallbacks`
- **职责**: 示例应用程序基类，管理完整的渲染应用生命周期

#### 虚函数（供子类重写）
| 方法签名 | 说明 |
|----------|------|
| `onLoad(pRenderContext)` | 上下文创建后调用 |
| `onShutdown()` | 上下文销毁前调用 |
| `onResize(width, height)` | 帧缓冲大小变更时调用 |
| `onFrameRender(pRenderContext, pTargetFbo)` | 每帧渲染回调 |
| `onGuiRender(pGui)` | UI 渲染回调 |
| `onOptionsChange()` | 设置选项变更回调 |
| `onHotReload(reloaded)` | 热重载回调（F5） |
| `onKeyEvent(keyEvent)` | 键盘事件回调 |
| `onMouseEvent(mouseEvent)` | 鼠标事件回调 |
| `onGamepadEvent(gamepadEvent)` | 手柄事件回调 |
| `onGamepadState(gamepadState)` | 手柄状态回调 |
| `onDroppedFile(path)` | 文件拖放回调 |

#### 公共方法
| 方法签名 | 说明 |
|----------|------|
| `run()` | 进入主循环 |
| `shutdown(returnCode)` | 关闭应用 |
| `renderFrame()` | 渲染单帧 |
| `resizeFrameBuffer(w, h)` | 调整帧缓冲大小 |
| `getDevice()` | 获取 GPU 设备 |
| `getTargetFbo()` | 获取当前帧缓冲 |
| `getWindow()` | 获取窗口 |
| `getGlobalClock()` | 获取全局时钟 |
| `getFrameRate()` | 获取帧率对象 |
| `toggleUI(showUI)` | 切换 UI 显示 |
| `pauseRenderer(pause)` | 暂停/恢复渲染 |
| `toggleVsync(on)` | 切换垂直同步 |

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `ref<Device>` | GPU 设备 |
| `mpWindow` | `ref<Window>` | 主窗口 |
| `mpSwapchain` | `ref<Swapchain>` | 交换链 |
| `mpTargetFBO` | `ref<Fbo>` | 目标帧缓冲 |
| `mClock` | `Clock` | 全局时钟 |
| `mFrameRate` | `FrameRate` | 帧率跟踪 |

## 依赖关系

### 本文件引用
- `Core/Window.h` — 窗口系统
- `Core/API/Device.h` — GPU 设备
- `Core/API/Swapchain.h` — 交换链
- `Core/HotReloadFlags.h` — 热重载标志
- `Core/Plugin.h` — 插件管理
- `Core/AssetResolver.h` — 资产路径解析
- `Core/Version.h` — 版本信息
- `Utils/Timing/FrameRate.h` — 帧率
- `Utils/UI/Gui.h` — GUI 系统
- `Utils/Scripting/Scripting.h` — 脚本系统

### 被以下文件引用
- Mogwai 及各示例程序

## 实现细节

- 构造函数按序初始化：日志输出版本信息 -> 启动 OS 服务和线程池 -> 创建 GPU 设备 -> 创建窗口和交换链 -> 配置资产搜索路径 -> 初始化 UI -> 加载所有插件。
- `renderFrame` 核心流程：检查时钟退出条件 -> 更新时钟和帧率 -> 调用 `onFrameRender` -> 渲染 UI -> 像素缩放 -> 性能分析 -> 截图 -> 复制到交换链图像并呈现。
- 渲染暂停时保存一份渲染输出副本，后续帧直接 blit 该副本。
- 内置快捷键：F2（切换UI）、F5（重载着色器）、F12（截图）、V（垂直同步）、Space/Pause（暂停时钟）、Ctrl+Space（暂停渲染）、ESC（退出）。
- 通过 `FALCOR_SCRIPT_BINDING` 注册 Python 绑定，提供 `exit`、`renderFrame`、`resizeFrameBuffer` 等脚本接口。
