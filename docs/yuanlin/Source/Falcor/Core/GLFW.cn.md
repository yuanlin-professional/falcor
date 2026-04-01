# GLFW 源码文档

> 路径: `Source/Falcor/Core/GLFW.h`
> 类型: C++ 头文件（仅头文件）
> 模块: Core

## 功能概述

GLFW 库的跨平台包含头文件。根据目标平台（Windows 或 Linux）自动配置 GLFW 的原生窗口系统支持，并清理 X11 头文件引入的宏污染。

## 平台配置

| 平台 | 宏定义 | 说明 |
|------|------|------|
| Windows | `GLFW_EXPOSE_NATIVE_WIN32` | 暴露 Win32 原生窗口句柄 |
| Linux | `GLFW_EXPOSE_NATIVE_X11` | 暴露 X11 原生窗口句柄 |

## 预处理器指令

- `GLFW_INCLUDE_NONE` — 禁止包含 GL/GLES 头文件（Falcor 使用自己的图形 API 层）
- Linux 平台下取消定义 `None`、`Bool`、`Status`、`Always` 等 X11 宏，避免与 Falcor 代码冲突

## 依赖关系

### 本文件引用
- `Core/Macros.h` — `FALCOR_WINDOWS`、`FALCOR_LINUX` 平台检测宏
- `GLFW/glfw3.h` — GLFW 核心头文件
- `GLFW/glfw3native.h` — GLFW 原生窗口系统头文件

### 被以下文件引用
- `Core/Window.cpp` — 窗口创建和事件处理
