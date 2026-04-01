# ProgressBar 源码文档

> 路径: `Source/Falcor/Core/Platform/ProgressBar.h` + `Source/Falcor/Core/Platform/ProgressBar.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/Platform

## 功能概述

提供跨平台的进度条窗口管理。在独立线程中创建和维护一个进度条视觉组件，用于向用户展示耗时操作的进行状态。头文件定义接口，`ProgressBar.cpp` 为空壳（仅包含命名空间），实际平台实现分别在 `Windows/ProgressBarWin.cpp` 和 `Linux/ProgressBarLinux.cpp` 中。

## 类与接口

### `ProgressBar`

- **继承**: 无
- **职责**: 创建、显示和管理进度条窗口及其后台线程

#### 内部结构体

| 结构体 | 说明 |
|--------|------|
| `Window` | 前向声明的内部结构体，封装进度条窗口和线程（平台特定实现） |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `ProgressBar()` | 构造函数 |
| `~ProgressBar()` | 析构函数，自动关闭进度条 |
| `void show(const std::string& msg)` | 显示进度条并显示指定消息 |
| `void close()` | 关闭进度条窗口 |
| `bool isActive() const` | 检查进度条是否正在活跃显示 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpWindow` | `std::unique_ptr<Window>` | 内部窗口对象指针，为空表示非活跃 |

## 依赖关系

### 本文件引用
- `Core/Macros.h` — 宏定义（`FALCOR_API`）

### 被以下文件引用
- `Windows/ProgressBarWin.cpp` — Windows 平台实现
- `Linux/ProgressBarLinux.cpp` — Linux 平台实现
- 框架中需要显示加载进度的模块

## 实现细节

- 使用 PIMPL（Pointer to Implementation）模式，通过 `std::unique_ptr<Window>` 隐藏平台特定实现。
- `show()` 调用前会先 `close()` 已有窗口，确保同一时刻只有一个进度条。
- `isActive()` 通过检查 `mpWindow` 是否为空来判断状态。
- 实际的窗口创建和线程管理逻辑由平台子目录的实现文件提供。
