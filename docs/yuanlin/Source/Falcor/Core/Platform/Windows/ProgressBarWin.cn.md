# ProgressBarWin 源码文档

> 路径: `Source/Falcor/Core/Platform/Windows/ProgressBarWin.cpp`
> 类型: C++ 实现文件（`ProgressBar` 类的 Windows 平台实现）
> 模块: Core/Platform/Windows

## 功能概述

提供 `ProgressBar` 类在 Windows 平台上的具体实现。使用 Win32 Common Controls 中的 `PROGRESS_CLASS` 控件创建滚动式进度条窗口，在独立线程中运行消息循环。

## 内部结构体

### `ProgressBar::Window`

- **职责**: 封装 Win32 进度条窗口和管理线程

| 成员 | 类型 | 说明 |
|------|------|------|
| `running` | `bool` | 控制线程运行的标志 |
| `thread` | `std::thread` | 进度条窗口线程 |

#### 方法

| 方法签名 | 说明 |
|----------|------|
| `Window(const std::string& msg)` | 构造函数，启动后台线程创建窗口 |
| `~Window()` | 析构函数，设置 `running=false` 并 join 线程 |
| `static void threadFunc(Window* pThis, std::string msgText)` | 线程函数，创建进度条窗口并运行消息循环 |

## 实现的 ProgressBar 方法

| 方法签名 | 说明 |
|----------|------|
| `ProgressBar::ProgressBar()` | 初始化 Win32 Common Controls（`ICC_PROGRESS_CLASS`） |
| `ProgressBar::~ProgressBar()` | 调用 `close()` |
| `void ProgressBar::show(const std::string& msg)` | 关闭已有窗口，创建新的 `Window` 实例 |
| `void ProgressBar::close()` | 重置 `mpWindow` 智能指针 |

## 依赖关系

### 本文件引用
- `../ProgressBar.h` — `ProgressBar` 类声明
- `../OS.h` — `setWindowIcon`、`getRuntimeDirectory` 等
- `<windows.h>` — Win32 API
- `<CommCtrl.h>` — Common Controls（进度条）
- `<thread>` — 线程

### 被以下文件引用
- 编译时链接，作为 `ProgressBar` 在 Windows 上的实现

## 实现细节

- 构造函数中调用 `InitCommonControlsEx` 初始化进度条控件类。
- 窗口使用 `PROGRESS_CLASS` 类创建，设置 `PBS_MARQUEE` 样式（滚动条模式）。
- 窗口大小为 200x60 像素，居中于屏幕。
- 使用 `setWindowIcon` 为窗口设置 NVIDIA 图标。
- 线程中每 50ms 发送 `PBM_STEPIT` 和 `WM_PAINT` 消息驱动进度条动画。
- 消息循环使用 `PeekMessage` 非阻塞方式处理窗口消息。
- 线程结束时调用 `DestroyWindow` 销毁窗口。
