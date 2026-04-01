# Window 源码文档

> 路径: `Source/Falcor/Core/Window.h` + `Source/Falcor/Core/Window.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core

## 功能概述

封装基于 GLFW 的跨平台窗口系统。`Window` 类负责创建和管理操作系统窗口，处理键盘、鼠标、手柄输入事件以及文件拖放，并提供消息循环驱动渲染帧。支持普通窗口、最小化和全屏三种模式。

## 类与接口

### `Window`
- **继承**: `Object`（引用计数）
- **职责**: 管理操作系统窗口及输入事件分发

#### 窗口模式枚举 `WindowMode`
| 值 | 说明 |
|------|------|
| `Normal` | 普通窗口 |
| `Minimized` | 最小化窗口 |
| `Fullscreen` | 全屏窗口 |

#### 配置结构体 `Desc`
| 成员 | 类型 | 说明 |
|------|------|------|
| `width` | `uint32_t` | 客户区宽度（默认 1920） |
| `height` | `uint32_t` | 客户区高度（默认 1080） |
| `title` | `string` | 窗口标题 |
| `mode` | `WindowMode` | 窗口模式 |
| `resizableWindow` | `bool` | 是否允许用户调整大小 |
| `enableVSync` | `bool` | 垂直同步 |

#### 回调接口 `ICallbacks`
| 方法 | 说明 |
|------|------|
| `handleWindowSizeChange()` | 窗口大小变更 |
| `handleRenderFrame()` | 渲染帧 |
| `handleKeyboardEvent(keyEvent)` | 键盘事件 |
| `handleMouseEvent(mouseEvent)` | 鼠标事件 |
| `handleGamepadEvent(gamepadEvent)` | 手柄事件 |
| `handleGamepadState(gamepadState)` | 手柄状态 |
| `handleDroppedFile(path)` | 文件拖放 |

#### 关键方法
| 方法签名 | 说明 |
|----------|------|
| `create(desc, pCallbacks)` | 静态工厂方法创建窗口 |
| `shutdown()` | 关闭窗口，导致 `msgLoop` 退出 |
| `shouldClose()` | 查询窗口是否应关闭 |
| `resize(width, height)` | 调整窗口大小 |
| `msgLoop()` | 进入消息循环（阻塞直到窗口关闭） |
| `pollForEvents()` | 轮询事件（含手柄输入） |
| `setWindowPos(x, y)` | 设置窗口位置 |
| `setWindowTitle(title)` | 设置窗口标题 |
| `setWindowIcon(path)` | 设置窗口图标 |
| `getApiHandle()` | 获取原生窗口句柄 |
| `getClientAreaSize()` | 获取客户区尺寸 |

#### 关键成员
| 成员 | 类型 | 说明 |
|------|------|------|
| `mpGLFWWindow` | `GLFWwindow*` | GLFW 窗口指针 |
| `mApiHandle` | `WindowHandle` | 平台原生窗口句柄 |
| `mMouseScale` | `float2` | 鼠标坐标归一化缩放因子 |
| `mpCallbacks` | `ICallbacks*` | 事件回调对象 |
| `mpGamepadData` | `unique_ptr<GamepadData>` | 手柄数据 |

### `ApiCallbacks`（内部类）
- **职责**: GLFW 回调的静态适配器，将 GLFW 事件转换为 Falcor 输入事件

## 依赖关系

### 本文件引用
- `Core/Macros.h` — 平台检测宏
- `Core/Object.h` — 引用计数基类
- `Core/Error.h` — 异常处理
- `Core/ObjectPython.h` — Python 绑定适配
- `Core/GLFW.h` — GLFW 头文件
- `Core/Platform/OS.h` — 平台工具函数
- `Core/Platform/PlatformHandles.h` — `WindowHandle` 类型
- `Utils/Math/Vector.h` — 向量类型
- `Utils/UI/InputTypes.h` — 输入事件类型
- `Utils/Scripting/ScriptBindings.h` — Python 绑定

### 被以下文件引用
- `Core/SampleApp.h` — 作为基类依赖
- `Core/Testbed.h` — 作为基类依赖

## 实现细节

- 使用全局原子计数器 `sWindowCount` 管理 GLFW 的初始化和终止，第一个窗口创建时调用 `glfwInit()`，最后一个窗口销毁时调用 `glfwTerminate()`。
- GLFW 键码通过 `glfwToFalcorKey` 映射为 Falcor 的 `Input::Key` 枚举。
- `fixGLFWModifiers` 修正 GLFW 在不同平台上修饰键报告不一致的问题。
- 鼠标坐标通过 `mMouseScale` 归一化到 [0, 1] 范围。
- 手柄支持自动检测连接/断开，注册 NVIDIA 控制器映射，并合成按钮按下/释放事件。
- 全屏模式使用无装饰窗口覆盖主显示器全部分辨率。
- 最小化模式先隐藏创建窗口，再最小化并显示。
- 提供 Python 绑定：`Window.set_window_pos`、`WindowMode` 枚举。
