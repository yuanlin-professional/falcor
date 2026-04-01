# ProgressBarLinux 源码文档

> 路径: `Source/Falcor/Core/Platform/Linux/ProgressBarLinux.cpp`
> 类型: C++ 实现文件（`ProgressBar` 类的 Linux 平台实现）
> 模块: Core/Platform/Linux

## 功能概述

提供 `ProgressBar` 类在 Linux 平台上的具体实现。使用 GTK 库创建进度条窗口，在独立线程中运行 GTK 事件循环，通过脉冲式动画（pulse）展示不确定时长的进度。

## 内部结构体

### `ProgressBar::Window`

- **职责**: 封装 GTK 进度条窗口和管理线程

| 成员 | 类型 | 说明 |
|------|------|------|
| `running` | `bool` | 控制线程运行的标志 |
| `thread` | `std::thread` | 进度条窗口线程 |

#### 方法

| 方法签名 | 说明 |
|----------|------|
| `Window(const std::string& msg)` | 构造函数，启动后台线程创建 GTK 窗口 |
| `~Window()` | 析构函数，设置 `running=false` 并等待线程结束 |
| `static void threadFunc(Window* pThis, std::string msg)` | 线程函数，创建并运行 GTK 进度条窗口 |
| `static gboolean pulseTimer(gpointer pGtkData)` | 定时回调，每 100ms 驱动进度条脉冲动画 |

## 实现的 ProgressBar 方法

| 方法签名 | 说明 |
|----------|------|
| `ProgressBar::ProgressBar()` | 默认构造 |
| `ProgressBar::~ProgressBar()` | 调用 `close()` |
| `void ProgressBar::show(const std::string& msg)` | 关闭已有窗口，初始化 GTK，创建新的 `Window` 实例 |
| `void ProgressBar::close()` | 重置 `mpWindow` 智能指针，触发窗口销毁 |

## 依赖关系

### 本文件引用
- `Core/Platform/ProgressBar.h` — `ProgressBar` 类声明
- `Core/Error.h` — 错误处理
- `<gtk/gtk.h>` — GTK 图形界面库
- `<thread>` / `<chrono>` — 线程和时间

### 被以下文件引用
- 编译时链接，作为 `ProgressBar` 在 Linux 上的实现

## 实现细节

- 进度条窗口无装饰（`gtk_window_set_decorated(FALSE)`），居中显示。
- 使用 `gtk_progress_bar_pulse()` 实现脉冲动画，而非精确百分比进度。
- `g_timeout_add(100, pulseTimer, pBar)` 每 100ms 触发一次脉冲。
- 线程函数中的事件循环使用 `gtk_main_iteration_do(FALSE)`（非阻塞），结合 10ms 的 sleep 来降低 CPU 占用。
- 窗口关闭时先调用 `gtk_window_close`，再移除定时器并销毁控件。
- `show()` 方法在创建窗口前会调用 `gtk_init_check` 确保 GTK 已初始化。
