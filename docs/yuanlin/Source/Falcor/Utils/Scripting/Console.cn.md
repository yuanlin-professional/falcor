# Console 源码文档

> 路径: `Source/Falcor/Utils/Scripting/Console.h` + `Source/Falcor/Utils/Scripting/Console.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/Scripting

## 功能概述

提供一个基于 ImGui 的脚本控制台界面，允许用户在运行时通过交互式命令行输入 Python 脚本命令。控制台支持命令历史记录浏览、自动滚动、以及与 Falcor 脚本引擎的集成。用户可通过反引号键（`` ` ``）切换控制台的显示/隐藏状态。

## 类与接口

### `Console`

- **继承**: 无
- **职责**: 管理控制台的渲染、用户输入处理、命令历史记录维护，以及脚本命令的执行。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `void clear()` | 清空控制台的日志内容 |
| `void render(Gui* pGui, bool& show)` | 渲染控制台界面并处理键盘输入事件。`` ` `` 键用于开关控制台，ESC 键用于关闭控制台，上下方向键用于浏览历史记录 |
| `bool flush()` | 处理待执行的命令。每帧结束时调用一次。输入 `cls` 可清屏。返回 `true` 表示执行了脚本处理 |

#### 私有方法

| 方法签名 | 说明 |
|----------|------|
| `void enterCommand()` | 将当前输入缓冲区的内容提交为待执行命令，并将其加入历史记录 |
| `std::optional<std::string> browseHistory(bool upOrDown)` | 浏览命令历史。`true` 表示向上（更早的命令），`false` 表示向下（更新的命令） |
| `static int inputTextCallback(ImGuiInputTextCallbackData* data)` | ImGui 输入框的回调函数，用于过滤反引号字符和处理历史记录浏览 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mLog` | `std::string` | 控制台日志内容（已执行命令的输出） |
| `mCmdBuffer` | `char[2048]` | 当前输入缓冲区 |
| `mCmdPending` | `std::string` | 待执行的命令字符串 |
| `mHistory` | `std::vector<std::string>` | 命令历史记录列表 |
| `mHistoryIndex` | `int32_t` | 当前历史记录浏览索引，-1 表示未浏览 |
| `mScrollToBottom` | `bool` | 是否需要在下次渲染时滚动到底部 |

### `ConsoleWindow`（匿名命名空间内部类）

- **继承**: 无
- **职责**: RAII 风格的 ImGui 窗口管理器，负责在构造时设置控制台窗口样式，在析构时恢复样式。

## 依赖关系

### 本文件引用

- `Core/Macros.h` — 导出宏定义
- `Core/Error.h` — 错误处理（`FALCOR_ASSERT`）
- `Utils/UI/InputTypes.h` — 输入类型定义
- `Utils/UI/Gui.h` — GUI 系统接口
- `Utils/Scripting/Scripting.h` — 脚本引擎（`Scripting::interpretScript`）
- `<imgui.h>` — ImGui 界面库

### 被以下文件引用

- 应用程序主循环中的 GUI 渲染逻辑

## 实现细节

- 控制台窗口固定在屏幕顶部，宽度占满屏幕，高度为 20 行文本。
- 背景色为半透明黑色（`rgba(0, 0, 0, 0.8)`）。
- 使用等宽字体（monospace）渲染所有文本。
- 命令历史记录自动去重（连续相同命令不重复记录）。
- `flush()` 方法通过 `Scripting::interpretScript` 执行命令，并捕获异常信息追加到日志中。
- 输入回调中过滤掉反引号字符，防止其出现在命令输入中。
