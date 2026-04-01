# FalcorPython.cpp 源码文档

> 路径: `Source/Falcor/FalcorPython.cpp`
> 类型: C++ 实现文件
> 模块: Falcor 根模块 — Python 绑定入口

## 功能概述

本文件是 Falcor 框架 Python 扩展模块 `falcor_ext` 的入口点。它使用 pybind11 的 `PYBIND11_MODULE` 宏定义 Python 模块，初始化日志系统、D3D12 Agility SDK 和插件管理器，然后通过 `ScriptBindings::initModule` 注册所有 Python 绑定。支持区分嵌入式 Python 环境（如 Mogwai）和独立 Python 环境两种加载模式。

## 函数

| 函数签名 | 说明 |
|----------|------|
| `static bool isLoadedFromEmbeddedPython()` | 检查模块是否从 Falcor 应用程序的嵌入式 Python 解释器加载。通过读取环境变量 `FALCOR_EMBEDDED_PYTHON` 判断，值为 `"1"` 时返回 `true` |
| `PYBIND11_MODULE(falcor_ext, m)` | Python 模块入口点。非嵌入模式下初始化日志输出（控制台+调试窗口）、启用 Agility SDK、加载所有插件；然后调用 `ScriptBindings::initModule` 注册绑定 |

## 依赖关系

### 本文件引用

- `Core/API/Device.h` — GPU 设备（`enableAgilitySDK`）
- `Core/Plugin.h` — 插件管理器
- `Utils/Scripting/ScriptBindings.h` — 脚本绑定注册
- `<pybind11/pybind11.h>` — pybind11 框架

## 实现细节

- 嵌入模式检测：通过 `os.environ["FALCOR_EMBEDDED_PYTHON"]` 环境变量判断。嵌入模式下跳过日志/Agility SDK/插件的初始化（由宿主应用已完成）
- 独立模式下依次执行：设置日志输出 -> 启用 D3D12 Agility SDK -> 加载所有插件
- 模块文档字符串设为 `"Falcor python bindings"`
