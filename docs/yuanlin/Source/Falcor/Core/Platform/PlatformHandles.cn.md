# PlatformHandles 源码文档

> 路径: `Source/Falcor/Core/Platform/PlatformHandles.h`
> 类型: C++ 头文件
> 模块: Core/Platform

## 功能概述

定义跨平台的操作系统句柄类型别名。为 Windows 和 Linux 平台提供统一的共享库句柄和窗口句柄类型，屏蔽平台差异。

## 类型定义

### Windows 平台

| 类型别名 | 实际类型 | 说明 |
|----------|----------|------|
| `SharedLibraryHandle` | `void*` | 共享库/DLL 句柄（对应 Windows `HMODULE`） |
| `WindowHandle` | `void*` | 窗口句柄（对应 Windows `HWND`） |

### Linux 平台

| 类型别名 | 实际类型 | 说明 |
|----------|----------|------|
| `SharedLibraryHandle` | `void*` | 共享库句柄（`dlopen` 返回值） |
| `WindowHandle` | 结构体 | X11 窗口句柄，包含 `pDisplay`（`void*`）和 `window`（`unsigned long`） |

### `WindowHandle`（Linux 结构体）

| 成员 | 类型 | 说明 |
|------|------|------|
| `pDisplay` | `void*` | X11 Display 指针 |
| `window` | `unsigned long` | X11 Window ID |

## 依赖关系

### 本文件引用
- `Core/Macros.h` — 平台检测宏（`FALCOR_WINDOWS`、`FALCOR_LINUX`）

### 被以下文件引用
- `OS.h` — 在操作系统抽象层中使用这些句柄类型
- 窗口管理、共享库加载等相关模块

## 实现细节

- 所有类型定义位于 `Falcor` 命名空间内。
- 在 Windows 上 `WindowHandle` 是简单的 `void*` 类型别名；在 Linux 上则是包含 X11 Display 和 Window 的结构体，以适应 X Window System 的双参数窗口标识方式。
- 若编译目标平台既非 Windows 也非 Linux，将触发编译错误 `"Platform not specified!"`。
