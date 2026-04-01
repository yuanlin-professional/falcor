# RenderGraphEditor 源码文档

> 路径: `Source/Tools/RenderGraphEditor/RenderGraphEditor.h`, `Source/Tools/RenderGraphEditor/RenderGraphEditor.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Tools/RenderGraphEditor

## 功能概述

这是 Falcor 的可视化渲染图编辑器应用程序。基于 `SampleApp` 框架，提供了一个带有 ImGui Dock 空间布局的图形界面，允许用户通过拖放操作创建和编辑渲染图（RenderGraph）。支持从 Python 脚本文件加载渲染图、验证图的合法性、序列化保存以及在 Mogwai 查看器中实时预览。

## 类与接口

### `RenderGraphEditor`

- **继承**: `SampleApp`
- **职责**: 渲染图编辑器的核心应用类，管理渲染图的创建、加载、编辑和序列化

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `onLoad(RenderContext*)` | 应用加载时初始化，加载插件和默认渲染图 |
| `onFrameRender(RenderContext*, const ref<Fbo>&)` | 每帧渲染回调，清除帧缓冲并更新渲染图 UI |
| `onResize(uint32_t, uint32_t)` | 窗口大小变化时调整渲染图和 UI 布局 |
| `onGuiRender(Gui*)` | 绘制 ImGui 界面，包括菜单、节点编辑器和设置面板 |
| `onDroppedFile(const path&)` | 处理拖放的 Python 脚本文件 |
| `createNewGraph(const string&)` | 创建空的渲染图 |
| `loadGraphsFromFile(const path&, const string&)` | 从文件加载渲染图 |
| `serializeRenderGraph(const path&)` | 将渲染图序列化保存到文件 |
| `deserializeRenderGraph(const path&)` | 从文件反序列化渲染图 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpGraphs` | `vector<ref<RenderGraph>>` | 已打开的渲染图列表 |
| `mRenderGraphUIs` | `vector<RenderGraphUI>` | 渲染图 UI 对象列表 |
| `mCurrentGraphIndex` | `size_t` | 当前活动的渲染图索引 |
| `mOptions` | `Options` | 应用启动选项 |
| `mViewerRunning` | `bool` | Mogwai 查看器是否正在运行 |
| `mUpdateFilePath` | `path` | 更新脚本的临时文件路径 |


## 结构体

- `Options`


## 依赖关系

### 本文件引用

- `Falcor.h`
- `Core/SampleApp.h`
- `RenderGraph/RenderGraph.h`
- `RenderGraph/RenderGraphUI.h`
- `RenderGraphEditor.h`
- `RenderGraph/RenderGraphImportExport.h`
- `GlobalState.h`
- `args.hxx`
- `imgui.h`
- `imgui_internal.h`
- `fstream`
- `filesystem`
- `windows.h`


## 实现细节

编辑器使用 ImGui Dock 空间进行窗口布局，分为四个主要区域：

1. **Graph Editor（图编辑器）**: 中央区域，显示渲染图的节点连接关系
2. **Render UI（渲染 UI）**: 右侧面板，显示渲染参数
3. **Graph Editor Settings（设置）**: 左下面板，提供图选择、验证和输出配置
4. **Render Passes（渲染通道）**: 下方面板，列出所有可用的渲染通道插件供拖放

支持通过 `--graph-file` 和 `--graph-name` 命令行参数指定要编辑的渲染图文件，也支持 `--editor` 标志从 Mogwai 启动的编辑器模式。
