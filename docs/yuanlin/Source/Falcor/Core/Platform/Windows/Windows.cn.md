# Windows 源码文档

> 路径: `Source/Falcor/Core/Platform/Windows/Windows.cpp`
> 类型: C++ 实现文件（实现 `OS.h` 中声明的 Windows 平台特定函数）
> 模块: Core/Platform/Windows

## 功能概述

提供 `OS.h` 中声明的各种操作系统抽象函数在 Windows 平台上的具体实现。使用 Win32 API 实现消息对话框（TaskDialog）、文件对话框（IFileDialog COM 接口）、进程管理、文件系统操作（Junction/重分析点）、线程控制、内存统计等功能。

文件开头导出 `NvOptimusEnablement = 1` 变量，确保在具有 NVIDIA Optimus 技术的笔记本上使用独立 GPU。

## 实现的函数

### 消息对话框

| 函数签名 | 说明 |
|----------|------|
| `MsgBoxButton msgBox(title, msg, type, icon)` | 将标准类型映射为自定义按钮后调用自定义版本 |
| `uint32_t msgBox(title, msg, buttons, icon, defaultButtonId)` | 使用 `TaskDialogIndirect` API 实现，支持自动计算对话框宽度以适应文本长度 |

### 文件对话框

| 函数签名 | 说明 |
|----------|------|
| `bool openFileDialog(filters, path)` | 使用 `IFileOpenDialog` COM 接口 |
| `bool saveFileDialog(filters, path)` | 使用 `IFileSaveDialog` COM 接口 |
| `bool chooseFolderDialog(path)` | 使用 `IFileOpenDialog` 带 `FOS_PICKFOLDERS` 标志 |

### 文件系统操作 (Junction)

| 函数签名 | 说明 |
|----------|------|
| `bool createJunction(link, target)` | 通过 `DeviceIoControl` + `FSCTL_SET_REPARSE_POINT` 创建 NTFS Junction（挂载点） |
| `bool deleteJunction(link)` | 通过 `DeviceIoControl` + `FSCTL_DELETE_REPARSE_POINT` 删除 Junction |

### 文件监控

| 函数签名 | 说明 |
|----------|------|
| `void monitorFileUpdates(path, callback)` | 使用 `ReadDirectoryChangesW` 在独立线程中监控文件修改 |
| `void closeSharedFile(path)` | 停止文件监控线程 |

### 进程管理

| 函数签名 | 说明 |
|----------|------|
| `size_t executeProcess(appName, args)` | 使用 `CreateProcessA` 启动进程 |
| `bool isProcessRunning(processID)` | 使用 `GetExitCodeProcess` 检查 `STILL_ACTIVE` |
| `void terminateProcess(processID)` | 使用 `TerminateProcess` |

### 目录与环境

| 函数签名 | 说明 |
|----------|------|
| `const path& getExecutablePath()` | 使用 `GetModuleFileNameA` |
| `const path& getRuntimeDirectory()` | 使用 `GetModuleHandleExA` + `GetModuleFileNameA` 获取 Falcor DLL 所在目录 |
| `const path& getAppDataDirectory()` | 使用 `SHGetKnownFolderPath(FOLDERID_LocalAppData)` |
| `const path& getHomeDirectory()` | 读取 `USERPROFILE` 环境变量 |
| `optional<string> getEnvironmentVariable(varName)` | 使用 `GetEnvironmentVariableA` |

### 窗口与显示

| 函数签名 | 说明 |
|----------|------|
| `void setWindowIcon(path, windowHandle)` | 使用 `LoadImageW` + `WM_SETICON` 消息 |
| `int getDisplayDpi()` | 使用 `GetDeviceCaps(LOGPIXELSX/Y)` |
| `float getDisplayScaleFactor()` | 基于 DPI 计算（DPI / 96.0） |

### 线程管理

| 函数签名 | 说明 |
|----------|------|
| `thread::native_handle_type getCurrentThread()` | 返回 `::GetCurrentThread()` |
| `void setThreadAffinity(thread, affinityMask)` | 使用 `SetThreadAffinityMask` |
| `void setThreadPriority(thread, priority)` | 使用 `SetThreadPriority`，支持后台模式（`THREAD_MODE_BACKGROUND_BEGIN/END`） |

### 内存统计

| 函数签名 | 说明 |
|----------|------|
| `uint64_t getTotalVirtualMemory()` | 使用 `GlobalMemoryStatusEx` |
| `uint64_t getUsedVirtualMemory()` | 使用 `GlobalMemoryStatusEx` 计算差值 |
| `uint64_t getProcessUsedVirtualMemory()` | 使用 `GetProcessMemoryInfo` 获取 `PrivateUsage` |
| `size_t getCurrentRSS()` | 使用 `GetProcessMemoryInfo` 获取 `WorkingSetSize` |
| `size_t getPeakRSS()` | 使用 `GetProcessMemoryInfo` 获取 `PeakWorkingSetSize` |

### 位操作

| 函数签名 | 说明 |
|----------|------|
| `uint32_t bitScanReverse(a)` | 使用 `_BitScanReverse` |
| `uint32_t bitScanForward(a)` | 使用 `_BitScanForward` |
| `uint32_t popcount(a)` | 使用 `__popcnt` |

### 共享库

| 函数签名 | 说明 |
|----------|------|
| `SharedLibraryHandle loadSharedLibrary(path)` | 使用 `LoadLibraryW` |
| `void releaseSharedLibrary(library)` | 使用 `FreeLibrary` |
| `void* getProcAddress(library, funcName)` | 使用 `GetProcAddress` |

### 调试工具

| 函数签名 | 说明 |
|----------|------|
| `bool isDebuggerPresent()` | 使用 `::IsDebuggerPresent()` |
| `void debugBreak()` | 使用 `__debugbreak()` |
| `void printToDebugWindow(s)` | 使用 `OutputDebugStringA` |

### OS 服务

| 函数签名 | 说明 |
|----------|------|
| `void OSServices::start()` | 初始化 COM 库（`CoInitializeEx`），支持引用计数 |
| `void OSServices::stop()` | 反初始化 COM（`CoUninitialize`），带引用计数保护 |

### 其他

| 函数签名 | 说明 |
|----------|------|
| `void setKeyboardInterruptHandler(handler)` | 使用 `SetConsoleCtrlHandler` 处理 `CTRL_C_EVENT` |
| `time_t getFileModifiedTime(path)` | 使用 `stat` 获取 `st_mtime` |

## 内部辅助类型

### `FilterSpec`

- **职责**: 将 `FileDialogFilterVec` 转换为 `COMDLG_FILTERSPEC` 数组供 COM 文件对话框使用

### `REPARSE_DATA_BUFFER`

- **职责**: 重分析点数据缓冲区结构体，用于创建 NTFS Junction

### `KeyboardInterruptData`

- **职责**: 单例模式管理 Ctrl-C 中断处理器，包含互斥锁和回调函数

## 依赖关系

### 本文件引用
- `Core/Platform/OS.h` — 函数声明
- `Core/Error.h` — 错误处理
- `Utils/Logger.h` — 日志
- `Utils/StringUtils.h` — 字符串转换（`string_2_wstring`、`wstring_2_string`）
- `<windows.h>` / `<commctrl.h>` / `<commdlg.h>` / `<psapi.h>` / `<shellscalingapi.h>` / `<ShlObj_core.h>` / `<winioctl.h>` — Win32 API

### 被以下文件引用
- 编译时链接到 Falcor 库中，提供 Windows 平台实现

## 实现细节

- **NvOptimusEnablement**: 导出 `NvOptimusEnablement = 1` 确保在 Optimus 笔记本上使用独立 NVIDIA GPU。
- **消息对话框**: 使用 `TaskDialogIndirect` 而非简单的 `MessageBox`，支持自定义按钮和自动宽度计算。通过 `GetTextExtentPoint32` 测量文本宽度，将像素转换为对话框单位。按钮 ID 映射到 `IDCANCEL + 1` 之后的范围以避免与系统 ID 冲突。
- **文件对话框**: 使用 COM `IFileOpenDialog` / `IFileSaveDialog` 接口，要求事先调用 `OSServices::start()` 初始化 COM。
- **Junction 创建**: 通过 `DeviceIoControl` 设置 `IO_REPARSE_TAG_MOUNT_POINT` 类型的重分析点数据，包含替代名称和打印名称。
- **文件监控**: 使用 `ReadDirectoryChangesW` 配合 `OVERLAPPED` 异步 I/O 在独立线程中监控目录变更，匹配文件名后触发回调。
- **OS 服务**: 使用引用计数机制，支持多次 `start()`/`stop()` 调用，仅在最后一次 `stop()` 时执行 `CoUninitialize`。
