# Windows 平台实现

## 功能概述

本目录包含 Falcor 渲染框架在 Windows 平台上的操作系统抽象层实现。这些文件为 `Core/Platform/OS.h` 和 `Core/Platform/ProgressBar.h` 中声明的跨平台接口提供了 Windows 特定的实现。

主要功能包括：

- **消息对话框**：基于 `TaskDialogIndirect` 的高级对话框系统，支持自动计算文本宽度以适配对话框尺寸，支持自定义按钮和图标
- **进程管理**：通过 `CreateProcessA` 启动外部进程，支持进程状态查询（`GetExitCodeProcess`）和进程终止（`TerminateProcess`）
- **文件系统操作**：
  - 基于 COM 的现代文件对话框（`IFileOpenDialog`/`IFileSaveDialog`）
  - NTFS 目录联接（Junction）创建与删除，使用 `DeviceIoControl` 和重解析点（Reparse Point）
  - 文件修改监控（`ReadDirectoryChangesW`），在独立线程中异步监听文件变更
- **路径查询**：通过 `GetModuleFileNameA` 获取可执行文件路径和运行时目录，通过 `SHGetKnownFolderPath` 获取应用数据目录
- **环境变量**：通过 `GetEnvironmentVariableA` 读取环境变量
- **线程管理**：线程亲和性设置（`SetThreadAffinityMask`）、线程优先级设置（`SetThreadPriority`），支持后台模式
- **内存查询**：虚拟内存总量、已用量、进程内存使用量（`GlobalMemoryStatusEx`、`GetProcessMemoryInfo`）、当前/峰值 RSS
- **调试支持**：`IsDebuggerPresent` 检测、`__debugbreak` 断点、`OutputDebugStringA` 输出
- **键盘中断处理**：通过 `SetConsoleCtrlHandler` 注册 Ctrl+C 处理器
- **位操作**：`_BitScanReverse`、`_BitScanForward`、`__popcnt` 的封装
- **共享库**：通过 `LoadLibraryW`/`FreeLibrary`/`GetProcAddress` 加载动态库
- **COM 服务**：`OSServices::start/stop` 管理 COM 库初始化（`CoInitializeEx`）
- **NVIDIA Optimus**：强制启用独立显卡（导出 `NvOptimusEnablement`）
- **进度条窗口**：基于 Win32 Common Controls 的 Marquee 进度条，在独立线程中运行

## 文件清单

| 文件名 | 类型 | 说明 |
|--------|------|------|
| `Windows.cpp` | 源文件 | Windows 平台 OS 接口的完整实现，包括对话框、进程管理、文件系统、联接点、线程、内存、调试等全部功能 |
| `ProgressBarWin.cpp` | 源文件 | 基于 Win32 Common Controls (`PROGRESS_CLASS`) 的 Marquee 风格进度条窗口实现 |

## 依赖关系

### 内部依赖
- `Core/Platform/OS.h` - 跨平台 OS 接口声明
- `Core/Platform/ProgressBar.h` - 进度条接口声明
- `Core/Error.h` - 错误处理宏
- `Utils/Logger.h` - 日志系统
- `Utils/StringUtils.h` - 字符串工具函数（宽字符转换等）

### 外部依赖
- **Windows API** - `windows.h`、`commctrl.h`、`commdlg.h`、`psapi.h`、`ShlObj_core.h`、`shellscalingapi.h`、`winioctl.h`
- **COM** - `IFileOpenDialog`、`IFileSaveDialog`、`TaskDialogIndirect`
- **comutil.h** - COM 工具函数
