# Linux 平台实现

## 功能概述

本目录包含 Falcor 渲染框架在 Linux 平台上的操作系统抽象层实现。这些文件为 `Core/Platform/OS.h` 和 `Core/Platform/ProgressBar.h` 中声明的跨平台接口提供了 Linux 特定的实现。

主要功能包括：

- **消息对话框**：基于 GTK 的模态消息对话框系统，支持多种按钮组合（确定、取消、重试、中止、忽略、是、否）
- **进程管理**：通过 `fork()`/`execv()` 启动外部进程
- **文件系统操作**：符号链接创建/删除、文件修改时间查询、文件打开/保存对话框（基于 GTK）
- **路径查询**：获取可执行文件路径（通过 `/proc/self/exe`）、运行时目录（通过 `dladdr`）、应用数据目录（`~/.falcor`）、主目录
- **环境变量**：读取系统环境变量
- **线程管理**：线程亲和性设置（`pthread_setaffinity_np`）、线程优先级设置（`pthread_setschedprio`）
- **调试支持**：调试器检测、调试断点（`SIGTRAP`）、调试输出窗口
- **键盘中断处理**：通过 `sigaction` 注册 `SIGINT` 信号处理器
- **位操作**：`bitScanReverse`、`bitScanForward`、`popcount` 的 GCC 内建函数封装
- **共享库**：通过 `dlopen`/`dlclose`/`dlsym` 加载动态库
- **进度条窗口**：基于 GTK 的进度条 UI，在独立线程中运行

## 文件清单

| 文件名 | 类型 | 说明 |
|--------|------|------|
| `Linux.cpp` | 源文件 | Linux 平台 OS 接口的完整实现，包括对话框、进程管理、文件系统、线程、调试等功能 |
| `ProgressBarLinux.cpp` | 源文件 | 基于 GTK 的进度条窗口实现，使用独立线程运行 GTK 事件循环 |

## 依赖关系

### 内部依赖
- `Core/Platform/OS.h` - 跨平台 OS 接口声明
- `Core/Platform/ProgressBar.h` - 进度条接口声明
- `Core/Error.h` - 错误处理宏（`FALCOR_THROW`、`FALCOR_UNIMPLEMENTED`）
- `Core/GLFW.h` - GLFW 窗口库封装（用于显示缩放因子）
- `Utils/Logger.h` - 日志系统
- `Utils/StringUtils.h` - 字符串工具函数

### 外部依赖
- **GTK 3** (`<gtk/gtk.h>`) - 用于消息对话框、文件对话框和进度条 UI
- **POSIX API** - `fork`、`execv`、`pthread`、`sigaction`、`dlopen` 等
- **GLFW** - 用于获取显示器内容缩放信息
