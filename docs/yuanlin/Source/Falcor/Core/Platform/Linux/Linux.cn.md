# Linux 源码文档

> 路径: `Source/Falcor/Core/Platform/Linux/Linux.cpp`
> 类型: C++ 实现文件（无对应头文件，实现 `OS.h` 中声明的 Linux 平台特定函数）
> 模块: Core/Platform/Linux

## 功能概述

提供 `OS.h` 中声明的各种操作系统抽象函数在 Linux 平台上的具体实现。使用 GTK 库实现消息框和文件对话框，使用 POSIX API 实现进程管理、文件系统操作、线程控制等功能，使用 GLFW 获取显示信息。

## 实现的函数

### 消息对话框

| 函数签名 | 说明 |
|----------|------|
| `MsgBoxButton msgBox(title, msg, type, icon)` | 将标准类型映射为自定义按钮后调用自定义版本 |
| `uint32_t msgBox(title, msg, buttons, icon, defaultButtonId)` | 使用 GTK `gtk_message_dialog_new` 实现自定义消息框 |

### 文件对话框

| 函数签名 | 说明 |
|----------|------|
| `bool openFileDialog(filters, path)` | 使用 GTK `gtk_file_chooser_dialog_new`（`ACTION_OPEN`） |
| `bool saveFileDialog(filters, path)` | 使用 GTK `gtk_file_chooser_dialog_new`（`ACTION_SAVE`） |
| `bool chooseFolderDialog(path)` | 未实现（`FALCOR_UNIMPLEMENTED`） |

### 进程管理

| 函数签名 | 说明 |
|----------|------|
| `size_t executeProcess(appName, args)` | 使用 `fork()` + `execv()` 启动子进程 |
| `bool isProcessRunning(processID)` | 未实现 |
| `void terminateProcess(processID)` | 未实现 |

### 文件系统操作

| 函数签名 | 说明 |
|----------|------|
| `void monitorFileUpdates(path, callback)` | 未实现 |
| `void closeSharedFile(path)` | 未实现 |
| `bool createJunction(link, target)` | 使用 `std::filesystem::create_directory_symlink` |
| `bool deleteJunction(link)` | 使用 `std::filesystem::remove` |

### 目录与环境

| 函数签名 | 说明 |
|----------|------|
| `const path& getExecutablePath()` | 通过 `/proc/self/exe` 符号链接获取 |
| `const path& getRuntimeDirectory()` | 通过 `dladdr()` 获取 Falcor 库所在目录 |
| `const path& getAppDataDirectory()` | 返回 `$HOME/.falcor` |
| `const path& getHomeDirectory()` | 返回 `$HOME` 环境变量值 |
| `optional<string> getEnvironmentVariable(varName)` | 使用 `getenv()` |
| `float getDisplayScaleFactor()` | 通过 `glfwGetMonitorContentScale` 获取 |

### 线程管理

| 函数签名 | 说明 |
|----------|------|
| `thread::native_handle_type getCurrentThread()` | 返回 `pthread_self()` |
| `void setThreadAffinity(thread, affinityMask)` | 使用 `pthread_setaffinity_np` |
| `void setThreadPriority(thread, priority)` | 使用 `pthread_setschedprio`，将枚举映射到系统优先级范围 |

### 调试工具

| 函数签名 | 说明 |
|----------|------|
| `bool isDebuggerPresent()` | 当前始终返回 false（ptrace 方案已注释） |
| `void debugBreak()` | 使用 `raise(SIGTRAP)` |
| `void printToDebugWindow(s)` | 输出到 `std::cerr` |

### 位操作

| 函数签名 | 说明 |
|----------|------|
| `uint32_t bitScanReverse(a)` | 使用 `__builtin_clz` |
| `uint32_t bitScanForward(a)` | 使用 `__builtin_ctz` |
| `uint32_t popcount(a)` | 使用 `__builtin_popcount` |

### 共享库

| 函数签名 | 说明 |
|----------|------|
| `SharedLibraryHandle loadSharedLibrary(path)` | 使用 `dlopen` |
| `void releaseSharedLibrary(library)` | 使用 `dlclose` |
| `void* getProcAddress(library, funcName)` | 使用 `dlsym` |

### 其他

| 函数签名 | 说明 |
|----------|------|
| `void setKeyboardInterruptHandler(handler)` | 使用 `sigaction` 处理 `SIGINT` |
| `time_t getFileModifiedTime(path)` | 使用 `stat` 获取 `st_mtime` |
| `void OSServices::start()` / `stop()` | 空实现（Linux 无需 COM 初始化） |
| `size_t getCurrentRSS()` / `getPeakRSS()` | 未实现 |

## 依赖关系

### 本文件引用
- `Core/Platform/OS.h` — 函数声明
- `Core/Error.h` — 错误处理宏
- `Core/GLFW.h` — GLFW 显示器 API
- `Utils/Logger.h` — 日志
- `Utils/StringUtils.h` — 字符串工具
- `<gtk/gtk.h>` — GTK 图形界面库
- `<dlfcn.h>` — 动态链接库操作
- `<signal.h>` — 信号处理
- `<pwd.h>` — 用户信息

### 被以下文件引用
- 编译时链接到 Falcor 库中，提供 Linux 平台实现

## 实现细节

- 多个函数标记为 `FALCOR_UNIMPLEMENTED()`，表示 Linux 平台尚未实现（文件监控、进程查询/终止、选择文件夹对话框、内存统计）。
- `KeyboardInterruptData` 使用单例模式和互斥锁保证线程安全。
- `threadErrorToString` 是辅助函数，将 pthread 错误码转换为可读字符串。
- `setThreadPriority` 将 `ThreadPriorityType` 枚举值线性映射到系统调度优先级范围。
- `executeProcess` 在 `fork()` 后的子进程中调用 `execv()`，父进程返回子进程 PID。
