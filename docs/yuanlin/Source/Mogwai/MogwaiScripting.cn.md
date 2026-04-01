# MogwaiScripting 源码文档

> 路径: `Source/Mogwai/MogwaiScripting.cpp`
> 类型: C++ 实现文件（无对应头文件，为 `Renderer` 类的成员函数实现）
> 模块: Mogwai

## 功能概述

本文件实现了 Mogwai 渲染器的 Python 脚本绑定系统和配置导出功能。通过 pybind11 将 `Renderer` 类的核心功能暴露给 Python 脚本，使用户能够通过脚本控制渲染器的场景加载、渲染图管理、窗口配置、时钟控制等所有关键操作。同时实现了将当前渲染器状态序列化为可重放 Python 脚本的功能。

## 类与接口

### `Renderer`（脚本绑定部分）

- **职责**: 为 `Renderer` 类注册 Python 绑定，实现配置导出为脚本。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `void Renderer::saveConfig(const std::filesystem::path& path) const` | 将当前渲染器完整状态（渲染图、场景、窗口配置、时钟、扩展设置）导出为 Python 脚本文件 |
| `void Renderer::registerScriptBindings(pybind11::module& m)` | 注册所有 Python 绑定，包括方法、属性、全局变量和帮助函数 |

## Python 脚本接口

### 全局变量

| 变量名 | 类型 | 说明 |
|--------|------|------|
| `m` | `Renderer` | Mogwai 渲染器实例 |
| `t` | `Clock*` | 全局时钟（已弃用） |
| `fc` | `Extension*` | 帧捕获扩展（已弃用） |
| `tc` | `Extension*` | 计时捕获扩展（已弃用） |

### 全局函数

| 函数 | 说明 |
|------|------|
| `renderFrame()` | 渲染一帧，会关闭进度条 |
| `help()` | 显示全局帮助信息 |
| `help(object)` | 显示指定对象的帮助信息 |
| `exit()` | 终止程序（Falcor 内建） |

### `Renderer` 类的 Python 方法

| Python 方法 | 说明 |
|-------------|------|
| `m.script(path)` | 执行 Python 脚本文件 |
| `m.loadScene(path, buildFlags=Default)` | 加载场景文件 |
| `m.unloadScene()` | 卸载当前场景 |
| `m.saveConfig(path)` | 保存当前配置为脚本 |
| `m.addGraph(graph)` | 添加渲染图 |
| `m.setActiveGraph(graph)` | 设置活动渲染图 |
| `m.removeGraph(name)` 或 `m.removeGraph(graph)` | 移除渲染图（按名称或引用） |
| `m.getGraph(name)` | 按名称获取渲染图 |
| `m.resizeFrameBuffer(width, height)` | 调整帧缓冲大小 |
| `m.renderFrame()` | 渲染一帧 |
| `m.getSettings()` | 获取设置对象 |
| `m.addOptions(dict)` | 添加选项并触发变更通知 |
| `m.addFilteredAttributes(dict/list)` | 添加过滤属性 |
| `m.clearOptions()` | 清除所有选项 |
| `m.clearFilteredAttributes()` | 清除所有过滤属性 |

### `Renderer` 类的 Python 属性

| Python 属性 | 类型 | 读写 | 说明 |
|-------------|------|------|------|
| `m.scene` | `Scene` | 只读 | 当前场景 |
| `m.activeGraph` | `RenderGraph*` | 只读 | 当前活动渲染图 |
| `m.clock` | `Clock*` | 只读 | 全局时钟 |
| `m.profiler` | `Profiler*` | 只读 | GPU 性能分析器 |
| `m.ui` | `bool` | 读写 | UI 显示/隐藏 |
| `m.keyCallback` | `KeyCallback` | 读写 | 自定义键盘回调 |
| `m.sceneUpdateCallback` | `SceneUpdateCallback` | 读写 | 场景更新回调 |

扩展也会动态注册为 `Renderer` 的只读属性（如 `m.frameCapture`、`m.timingCapture`）。

## 依赖关系

### 本文件引用

- `Falcor.h` — Falcor 核心
- `Mogwai.h` — Renderer 类定义
- `RenderGraph/RenderGraphIR.h` — 渲染图中间表示
- `RenderGraph/RenderGraphImportExport.h` — 渲染图导入导出
- `Utils/Scripting/Scripting.h` — 脚本引擎
- `Utils/Scripting/ScriptWriter.h` — 脚本代码生成工具
- `Utils/Settings/Settings.h` — 设置系统
- `<pybind11/pybind11.h>` — Python 绑定库

### 被以下文件引用

- 本文件为 `Renderer` 类的方法实现，不被其他文件直接引用

## 实现细节

- **配置导出格式**: `saveConfig()` 生成的脚本包含五个部分：渲染图定义（IR 格式）、场景加载、窗口配置、时钟设置、扩展配置。每个部分以注释分隔。
- **脚本变量命名**: 渲染器在脚本中的变量名固定为 `m`，扩展的脚本变量通过各自的 `getScriptVar()` 返回。
- **弃用兼容**: 保留了 `t`（时钟）、`fc`（帧捕获）、`tc`（计时捕获）、`resizeSwapChain` 等旧式全局变量和方法名，标记为 `PYTHONDEPRECATED`。
- **帮助系统**: 覆盖了 Python 的 `help()` 函数，无参版本显示 Mogwai 专用帮助信息，带参版本委托给 Python 内建 `builtins.help()`。
- **扩展注册**: 在 `registerScriptBindings()` 中遍历所有扩展，调用其 `registerScriptBindings()` 方法，并为有脚本变量的扩展注册只读属性。
