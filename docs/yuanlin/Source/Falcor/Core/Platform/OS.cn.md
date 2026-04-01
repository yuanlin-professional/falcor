# OS 源码文档

> 路径: `Source/Falcor/Core/Platform/OS.h` + `Source/Falcor/Core/Platform/OS.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/Platform

## 功能概述

提供 Falcor 框架的核心操作系统抽象层，包含大量跨平台的工具函数和类。涵盖以下功能领域：
- OS 服务初始化/关闭
- 消息对话框（含自定义按钮）
- 文件对话框（打开/保存/选择文件夹）
- 文件路径查找与搜索（着色器目录、数据目录）
- 文件系统操作（临时文件、软链接/Junction、文件监控）
- 进程管理（启动/查询/终止）
- 环境变量与目录查询
- 线程管理（亲和性、优先级）
- 内存统计
- 位操作工具
- 共享库加载
- 调试工具（断点、调试输出、堆栈跟踪）
- 文件读取与解压（gzip）

平台特定的实现分别位于 `Windows/Windows.cpp` 和 `Linux/Linux.cpp` 中。

## 类与接口

### `OSServices`

- **继承**: 无
- **职责**: 管理操作系统服务的启动与关闭

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static void start()` | 启动 OS 服务（Windows 上初始化 COM 库） |
| `static void stop()` | 关闭 OS 服务（Windows 上反初始化 COM） |

### `FileDialogFilter`

- **职责**: 文件对话框的扩展名过滤器

| 成员 | 类型 | 说明 |
|------|------|------|
| `desc` | `std::string` | 描述（如 "Portable Network Graphics"） |
| `ext` | `std::string` | 扩展名，不含 `.`（如 "png"） |

### `MsgBoxCustomButton`

- **职责**: 自定义消息框按钮

| 成员 | 类型 | 说明 |
|------|------|------|
| `id` | `uint32_t` | 按钮标识（返回码） |
| `title` | `std::string` | 按钮标题 |

## 枚举类型

| 枚举 | 说明 |
|------|------|
| `MsgBoxIcon` | 消息框图标：None / Info / Warning / Error |
| `MsgBoxType` | 消息框类型：Ok / OkCancel / RetryCancel / AbortRetryIgnore / YesNo |
| `MsgBoxButton` | 消息框按钮返回值：Ok / Retry / Cancel / Abort / Ignore / Yes / No |
| `ThreadPriorityType` | 线程优先级：BackgroundBegin / BackgroundEnd / Lowest / Low / Normal / High / Highest |

## 关键自由函数

### 窗口与显示

| 函数签名 | 说明 |
|----------|------|
| `void setWindowIcon(const path&, WindowHandle)` | 为窗口设置图标 |
| `int getDisplayDpi()` | 获取显示器 DPI |
| `float getDisplayScaleFactor()` | 获取显示缩放因子 |

### 消息对话框

| 函数签名 | 说明 |
|----------|------|
| `MsgBoxButton msgBox(title, msg, type, icon)` | 显示标准消息框 |
| `uint32_t msgBox(title, msg, buttons, icon, defaultButtonId)` | 显示自定义按钮消息框 |

### 文件路径查找

| 函数签名 | 说明 |
|----------|------|
| `bool isSamePath(lhs, rhs)` | 比较两个路径是否指向同一位置（Windows 大小写不敏感） |
| `path findFileInDirectories(path, directories)` | 在目录列表中查找文件 |
| `vector<path> globFilesInDirectory(path, regex, firstMatchOnly)` | 在单个目录中按正则匹配文件 |
| `vector<path> globFilesInDirectories(path, regex, dirs, firstMatchOnly)` | 在多个目录中按正则匹配文件 |
| `bool findFileInShaderDirectories(path, fullPath)` | 在着色器目录中查找文件 |
| `const vector<path>& getShaderDirectoriesList()` | 获取着色器搜索目录列表 |

### 文件对话框

| 函数签名 | 说明 |
|----------|------|
| `bool openFileDialog(filters, path)` | 打开"打开文件"对话框 |
| `bool saveFileDialog(filters, path)` | 打开"保存文件"对话框 |
| `bool chooseFolderDialog(path)` | 打开"选择文件夹"对话框 |

### 文件系统操作

| 函数签名 | 说明 |
|----------|------|
| `void monitorFileUpdates(path, callback)` | 监控文件修改并回调 |
| `void closeSharedFile(path)` | 关闭文件监控 |
| `path getTempFilePath()` | 生成唯一临时文件路径 |
| `bool createJunction(link, target)` | 创建软链接/Junction |
| `bool deleteJunction(link)` | 删除软链接/Junction |
| `string readFile(path)` | 读取文件内容到字符串 |
| `string decompressFile(path)` | 读取并解压 .gz 文件 |

### 进程管理

| 函数签名 | 说明 |
|----------|------|
| `size_t executeProcess(appName, args)` | 启动外部进程 |
| `bool isProcessRunning(processID)` | 检查进程是否运行中 |
| `void terminateProcess(processID)` | 终止进程 |

### 目录与环境

| 函数签名 | 说明 |
|----------|------|
| `const path& getProjectDirectory()` | 获取 Falcor 项目目录（仅开发模式） |
| `const path& getExecutablePath()` | 获取当前可执行文件完整路径 |
| `const path& getExecutableDirectory()` | 获取可执行文件所在目录 |
| `const string& getExecutableName()` | 获取可执行文件名 |
| `const path& getRuntimeDirectory()` | 获取 Falcor 运行时目录 |
| `const path& getAppDataDirectory()` | 获取应用数据目录 |
| `const path& getHomeDirectory()` | 获取用户主目录 |
| `optional<string> getEnvironmentVariable(varName)` | 获取环境变量值 |
| `bool isDevelopmentMode()` | 检查是否为开发模式（FALCOR_DEVMODE=1） |

### 线程管理

| 函数签名 | 说明 |
|----------|------|
| `thread::native_handle_type getCurrentThread()` | 获取当前线程句柄 |
| `void setThreadAffinity(thread, affinityMask)` | 设置线程 CPU 亲和性掩码 |
| `void setThreadPriority(thread, priority)` | 设置线程优先级 |

### 内存统计

| 函数签名 | 说明 |
|----------|------|
| `uint64_t getTotalVirtualMemory()` | 获取系统虚拟内存总量 |
| `uint64_t getUsedVirtualMemory()` | 获取系统已用虚拟内存 |
| `uint64_t getProcessUsedVirtualMemory()` | 获取当前进程虚拟内存用量 |
| `uint64_t getCurrentRSS()` | 获取当前驻留集大小 |
| `uint64_t getPeakRSS()` | 获取峰值驻留集大小 |

### 位操作与调试

| 函数签名 | 说明 |
|----------|------|
| `uint32_t bitScanReverse(uint32_t)` | 返回最高有效位索引 |
| `uint32_t bitScanForward(uint32_t)` | 返回最低有效位索引 |
| `uint32_t popcount(uint32_t)` | 返回置位比特数 |
| `bool isDebuggerPresent()` | 检查是否有调试器连接 |
| `void debugBreak()` | 触发调试断点 |
| `void printToDebugWindow(const string&)` | 输出到调试窗口 |
| `string getStackTrace(skip, maxDepth)` | 获取堆栈跟踪字符串 |

### 共享库

| 函数签名 | 说明 |
|----------|------|
| `SharedLibraryHandle loadSharedLibrary(path)` | 加载动态链接库 |
| `void releaseSharedLibrary(library)` | 释放动态链接库 |
| `void* getProcAddress(library, funcName)` | 获取库中函数指针 |

### 其他

| 函数签名 | 说明 |
|----------|------|
| `path findAvailableFilename(prefix, dir, ext)` | 在目录中查找可用的文件名 |
| `void setKeyboardInterruptHandler(handler)` | 设置 Ctrl-C 中断处理器 |
| `bool hasExtension(path, ext)` | 检查文件是否有指定扩展名（大小写不敏感） |
| `string getExtensionFromPath(path)` | 获取文件扩展名（小写，不含 `.`） |
| `time_t getFileModifiedTime(path)` | 获取文件最后修改时间 |

## 依赖关系

### 本文件引用
- `PlatformHandles.h` — 平台句柄类型定义
- `Core/Macros.h` — 宏定义
- `Core/Error.h` — 错误处理
- `Utils/StringUtils.h` / `Utils/StringFormatters.h` — 字符串工具
- `<backward/backward.hpp>` — 堆栈跟踪（替代 C++20 `<stacktrace>`）
- `<zlib.h>` — gzip 解压

### 被以下文件引用
- 框架中几乎所有模块都依赖此文件提供的操作系统抽象功能

## 实现细节

- `OS.cpp` 包含跨平台通用实现（路径查找、着色器目录管理、文件读取/解压、堆栈跟踪等）。
- 平台相关实现（消息框、文件对话框、进程管理、线程操作等）分别在 `Windows/Windows.cpp` 和 `Linux/Linux.cpp` 中。
- 着色器目录在开发模式下搜索源码目录，在部署模式下搜索运行时 `shaders` 目录。
- 数据目录通过 `FALCOR_MEDIA_FOLDERS` 环境变量配置，以分号分隔。
- `getStackTrace()` 使用 backward-cpp 库实现，输出格式兼容 IDE 的源码定位（`file:line`）。
- `decompressFile()` 使用 zlib 库，支持 zlib 和 gzip 两种格式。
