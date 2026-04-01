# DebugConsole.h 源码文档
> 路径: `Source/Falcor/Utils/Debug/DebugConsole.h`
> 类型: C++ 头文件（仅头文件实现）
> 模块: Utils/Debug

## 功能概述

提供一个调试控制台窗口工具类，用于在 Windows 平台上打开一个独立的控制台窗口，并将 `std::cout`、`std::cerr` 和 `std::cin` 重定向到该控制台。对象销毁时自动关闭控制台并恢复流的原始状态。该类仅在 `FALCOR_WINDOWS` 宏定义下可用。

## 类与接口

### `DebugConsole`

- **继承**: 无
- **职责**: 管理一个 Windows 调试控制台窗口的生命周期，在构造时打开控制台并重定向标准流，在析构时恢复流并关闭控制台。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `DebugConsole(bool waitForKey = true)` | 构造函数。调用 `AllocConsole()` 打开控制台窗口，将 `cout`/`cerr`/`cin` 重定向至控制台。`waitForKey` 控制析构时是否等待按键。 |
| `virtual ~DebugConsole()` | 析构函数。刷新流、可选等待按键、恢复原始流缓冲区、调用 `FreeConsole()` 关闭控制台。 |
| `void pause() const` | 输出提示信息 "Press any key to continue..." 并等待用户按键输入。 |
| `void flush() const` | 刷新 `std::cout` 和 `std::cerr` 的缓冲区。 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mCout` | `std::ofstream` | 重定向后的控制台输出流（CONOUT$） |
| `mCerr` | `std::ofstream` | 重定向后的控制台错误流（CONERR$） |
| `mCin` | `std::ifstream` | 重定向后的控制台输入流（CONIN$） |
| `mPrevCout` | `std::streambuf*` | 保存的原始 `cout` 流缓冲区指针 |
| `mPrevCerr` | `std::streambuf*` | 保存的原始 `cerr` 流缓冲区指针 |
| `mPrevCin` | `std::streambuf*` | 保存的原始 `cin` 流缓冲区指针 |
| `mWaitForKey` | `bool` | 控制析构时是否等待用户按键，默认为 `true` |

## 依赖关系

### 本文件引用

- `Core/Macros.h` — 用于 `FALCOR_WINDOWS` 平台宏判断
- `<windows.h>` — Windows API（`AllocConsole`、`FreeConsole`）
- `<fstream>` — 文件流（用于重定向）
- `<iostream>` — 标准输入输出流

### 被以下文件引用

- 可在任何需要调试输出的 Windows 宿主代码中使用

## 实现细节

- 该类完全在头文件中内联实现，无对应 `.cpp` 文件。
- 使用 `#if FALCOR_WINDOWS` 条件编译，仅在 Windows 平台下编译。
- 构造时通过 `rdbuf()` 交换机制将 C++ 标准流重定向到 Windows 控制台设备（`CONOUT$`、`CONERR$`、`CONIN$`）。
- 注释中包含被注释掉的 `freopen_s` 方式（用于重定向 C 风格 `stdout`），当前未启用。
- 析构顺序严格保证：先刷新 → 可选暂停 → 恢复流 → 释放控制台。
