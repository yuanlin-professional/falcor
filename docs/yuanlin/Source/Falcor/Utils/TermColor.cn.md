# TermColor 源码文档

> 路径: `Source/Falcor/Utils/TermColor.h` + `Source/Falcor/Utils/TermColor.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils

## 功能概述

提供终端彩色输出支持。根据输出流是否为终端（TTY），自动决定是否使用 ANSI 转义码为字符串添加颜色。支持 Windows 和 Linux 平台。

## 类与接口

### `TermColor` 枚举

| 值 | 说明 |
|----|------|
| `Gray` | 灰色 |
| `Red` | 红色 |
| `Green` | 绿色 |
| `Yellow` | 黄色 |
| `Blue` | 蓝色 |
| `Magenta` | 品红色 |

### 自由函数

| 函数签名 | 说明 |
|----------|------|
| `string colored(const string& str, TermColor color, const ostream& stream)` | 如果流是终端则添加颜色码，否则返回原始字符串 |

## 依赖关系

### 本文件引用

- `Core/Macros.h` — 平台检测宏
- Windows: `<windows.h>`、`<io.h>`
- Linux: `<unistd.h>`

### 被以下文件引用

- 日志系统、命令行输出等需要彩色终端输出的模块

## 实现细节

- Windows 平台通过 `SetConsoleMode` 启用虚拟终端处理（ANSI 支持），在静态初始化时执行
- 使用 `_isatty`/`isatty` 检测输出流是否为终端
- ANSI 颜色码使用 90-95 范围的亮色（bright colors）
- 仅支持 `stdout` 和 `stderr` 的 TTY 检测
