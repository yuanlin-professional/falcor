# Testbed 源码文档

> 路径: `Source/Falcor/Core/Testbed.h` + `Source/Falcor/Core/Testbed.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core

## 功能概述

Falcor 测试台应用类，是通过 Python API 使用 Falcor 的主要入口。与 `SampleApp` 不同，`Testbed` 设计为可从 Python 脚本完全控制的轻量级应用框架，支持场景加载、渲染图管理、单帧渲染、输出捕获等功能，适用于自动化测试和脚本驱动的渲染工作流。

## 类与接口

### `Testbed`
- **继承**: `Object`（引用计数）、`Window::ICallbacks`（私有继承）
- **职责**: 可脚本控制的 Falcor 应用主类

#### Options 配置结构体
| 成员 | 类型 | 说明 |
|------|------|------|
| `pDevice` | `ref<Device>` | 可复用已有 GPU 设备 |
| `deviceDesc` | `Device::Desc` | 设备创建配置 |
| `windowDesc` | `Window::Desc` | 窗口配置 |
| `createWindow` | `bool` | 是否创建窗口 |
| `showFPS` | `bool` | 是否显示帧率 |
| `colorFormat` | `ResourceFormat` | 帧缓冲颜色格式 |
| `depthFormat` | `ResourceFormat` | 深度缓冲区格式 |

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `create(options)` | 静态工厂方法 |
| `run()` | 运行主循环（窗口关闭或调用 `interrupt()` 时返回） |
| `interrupt()` | 中断主循环 |
| `close()` | 关闭测试台 |
| `frame()` | 渲染单帧 |
| `resizeFrameBuffer(w, h)` | 调整帧缓冲大小 |
| `loadScene(path, buildFlags)` | 加载场景文件 |
| `loadSceneFromString(str, ext, flags)` | 从字符串加载场景 |
| `createRenderGraph(name)` | 创建新渲染图 |
| `loadRenderGraph(path)` | 从文件加载渲染图 |
| `setRenderGraph(graph)` / `getRenderGraph()` | 设置/获取活动渲染图 |
| `captureOutput(path, outputIndex)` | 捕获渲染图输出并保存到文件 |
| `setRenderTexture(texture)` | 设置要显示的纹理（覆盖渲染图输出） |
| `setShowUI(showUI)` / `getShowUI()` | UI 可见性控制 |
| `shouldClose()` | 检查是否应关闭 |

#### 回调属性
| 成员 | 类型 | 说明 |
|------|------|------|
| `mKeyboardEventCallback` | `function<bool(KeyboardEvent)>` | 键盘事件回调 |
| `mMouseEventCallback` | `function<bool(MouseEvent)>` | 鼠标事件回调 |
| `mWindowSizeChangeCallback` | `function<void(uint32_t, uint32_t)>` | 窗口大小变更回调 |

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `ref<Device>` | GPU 设备 |
| `mpScene` | `ref<Scene>` | 当前场景 |
| `mpRenderGraph` | `ref<RenderGraph>` | 活动渲染图 |
| `mpRenderTexture` | `ref<Texture>` | 覆盖显示纹理 |
| `mpScreen` | `ref<python_ui::Screen>` | Python UI 屏幕 |

## 依赖关系

### 本文件引用
- `Core/Window.h` — 窗口系统
- `Core/Object.h` — 引用计数基类
- `Core/API/Device.h`、`Core/API/Swapchain.h` — GPU 资源
- `RenderGraph/RenderGraph.h` — 渲染图
- `Scene/Scene.h`、`Scene/SceneBuilder.h` — 场景
- `Utils/Image/ImageProcessing.h` — 图像通道提取
- `Utils/UI/PythonUI.h` — Python UI

### 被以下文件引用
- Python 脚本通过 `falcor.Testbed` 使用

## 实现细节

- `frame()` 核心流程：更新时钟/帧率 -> 轮询窗口事件 -> 清空帧缓冲 -> 编译渲染图 -> 更新场景 -> 执行渲染图 -> blit 输出到帧缓冲 -> blit 覆盖纹理 -> 渲染 UI -> 复制到交换链并呈现。
- `captureOutput` 支持按通道掩码（R/G/B/A/RGB/RGBA）分别导出渲染图输出，自动处理格式转换。
- 提供丰富的 Python 绑定，包括 `Testbed` 构造函数（支持大量可选参数）、所有公共方法和属性，以及 `MouseEvent`、`KeyboardEvent` 等输入事件类型。
- 内置快捷键：ESC（中断并关闭）、F1（帮助）、F2（切换UI）、F5（重载着色器）、P（性能分析器）。
- 支持复用已有 `Device` 实例，适用于测试场景中共享设备的情况。
