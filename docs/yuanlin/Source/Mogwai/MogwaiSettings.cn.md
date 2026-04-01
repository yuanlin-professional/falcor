# MogwaiSettings 源码文档

> 路径: `Source/Mogwai/MogwaiSettings.h`, `Source/Mogwai/MogwaiSettings.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Mogwai

## 功能概述

`MogwaiSettings` 是 Mogwai 的内建扩展，负责渲染应用程序的主菜单栏、图形UI面板、FPS显示、时间控制窗口、窗口大小设置等所有核心用户界面元素。它处理与UI相关的键盘和鼠标快捷键，并支持通过 Settings 选项系统动态配置 UI 状态。

## 类与接口

### `MogwaiSettings`

- **继承**: `Extension`
- **职责**: 提供 Mogwai 应用程序的完整用户界面，包括主菜单、渲染图面板、FPS 叠加层、时间控制、窗口大小设置和脚本控制台。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static UniquePtr create(Renderer* pRenderer)` | 工厂方法，创建 `MogwaiSettings` 实例 |
| `void renderUI(Gui* pGui) override` | 主 UI 渲染入口，依次渲染主菜单、渲染图面板、FPS、时间、窗口大小和控制台 |
| `bool mouseEvent(const MouseEvent& e) override` | 鼠标事件处理，记录鼠标位置（用于菜单自动隐藏） |
| `bool keyboardEvent(const KeyboardEvent& e) override` | 键盘事件处理，支持 F1/F6/F7/F9/F10/F11/N 快捷键和 Ctrl+O/Ctrl+Shift+O |
| `bool gamepadEvent(const GamepadEvent& e) override` | 手柄事件处理，Y 键切换下一个渲染图 |
| `void onOptionsChange(const Settings::Options& settings) override` | 响应选项变更，从设置中读取 UI 状态 |
| `void renderMainMenu(Gui* pGui)` | 渲染主菜单栏（File、View、Help） |
| `void renderOverlay(Gui* pGui)` | 渲染叠加层 UI |
| `void renderGraphs(Gui* pGui)` | 渲染渲染图面板：图选择、输出选择、编辑器按钮、框架统计、场景设置、通道 UI |
| `void renderTimeSettings(Gui* pGui)` | 渲染时间控制窗口，显示时钟 UI 和退出倒计时 |
| `void renderWindowSettings(Gui* pGui)` | 渲染窗口大小设置，支持预设分辨率和自定义分辨率 |
| `void selectNextGraph()` | 切换到下一个渲染图（循环切换） |
| `void exitIfNeeded()` | 检查是否需要退出 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mAutoHideMenu` | `bool` | 主菜单自动隐藏（鼠标离开顶部时隐藏） |
| `mShowFps` | `bool` | 是否显示 FPS 叠加层（默认开启） |
| `mShowOverlayUI` | `bool` | 是否显示叠加层 UI（默认开启） |
| `mShowGraphUI` | `bool` | 是否显示渲染图面板（默认开启） |
| `mShowConsole` | `bool` | 是否显示脚本控制台 |
| `mShowTime` | `bool` | 是否显示时间控制窗口 |
| `mShowWinSize` | `bool` | 是否显示窗口大小设置 |
| `mMousePosition` | `uint2` | 当前鼠标屏幕位置 |

## 快捷键

| 按键 | 功能 |
|------|------|
| F1 | 显示帮助信息 |
| F6 | 切换渲染图 UI 显示 |
| F7 | 切换叠加层 UI 显示 |
| F9 | 切换时间控制窗口 |
| F10 | 切换 FPS 显示 |
| F11 | 切换菜单自动隐藏 |
| N | 切换下一个渲染图 |
| Ctrl+O | 打开脚本加载对话框 |
| Ctrl+Shift+O | 打开场景加载对话框 |
| ` (反引号) | 切换脚本控制台 |

## 可配置选项

通过 `Settings::Options` 系统（`m.addOptions()`）可配置以下属性：

| 选项键 | 类型 | 说明 |
|--------|------|------|
| `MogwaiSettings:mAutoHideMenu` | `bool` | 菜单自动隐藏 |
| `MogwaiSettings:mShowFps` | `bool` | FPS 显示 |
| `MogwaiSettings:mShowGraphUI` | `bool` | 渲染图面板显示 |
| `MogwaiSettings:mShowConsole` | `bool` | 控制台显示 |
| `MogwaiSettings:mShowTime` | `bool` | 时间窗口显示 |
| `MogwaiSettings:mShowWinSize` | `bool` | 窗口大小设置显示 |

## 依赖关系

### 本文件引用

- `Mogwai.h` — Renderer 和 Extension 基类
- `Falcor.h` — Falcor 框架核心
- `Core/Program/ProgramManager.h` — 着色器编译统计
- `Utils/Scripting/Console.h` — 脚本控制台
- `Utils/Settings/Settings.h` — 选项系统

### 被以下文件引用

- `Source/Mogwai/Mogwai.cpp` — 在 `onLoad()` 中作为第一个扩展创建

## 实现细节

- **菜单自动隐藏**: 当 `mAutoHideMenu` 为 true 且鼠标 Y 坐标 >= 20 时，不渲染主菜单。
- **预设分辨率**: 支持 1280x720、1920x1080、1920x1200、2560x1440、3840x2160 五种预设分辨率，以及自定义分辨率输入。
- **渲染图面板**: 包含渲染图下拉选择、编辑/删除按钮、输出选择、"显示所有输出"选项、调试窗口管理、框架编译统计、场景设置面板和渲染通道 UI。
- **框架统计**: 显示 Slang 着色器编译的程序版本数、内核数、总时间、最大时间等详细信息，支持重置统计。
- **渲染图切换**: `selectNextGraph()` 使用取模运算实现循环切换，支持手柄 Y 键和键盘 N 键触发。
