# Mogwai 源码文档

> 路径: `Source/Mogwai/Mogwai.h`, `Source/Mogwai/Mogwai.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Mogwai

## 功能概述

这是 Mogwai 渲染应用程序的核心文件，定义了 `Extension` 扩展基类和 `Renderer` 主渲染器类。`Renderer` 继承自 Falcor 的 `SampleApp`，负责渲染图（RenderGraph）的管理与执行、场景加载与更新、用户输入处理、扩展机制管理，以及应用程序的完整生命周期。该文件同时包含 `main()` 入口函数和命令行参数解析。

## 类与接口

### `Extension`

- **继承**: 无（作为基类被其他扩展继承）
- **职责**: 定义 Mogwai 扩展的统一接口。扩展可以在帧的起止处执行逻辑、渲染 UI、处理输入事件、注册 Python 脚本绑定，以及响应渲染图的增删和切换。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `virtual const std::string& getName() const` | 返回扩展名称 |
| `virtual void beginFrame(RenderContext*, const ref<Fbo>&)` | 每帧开始时调用 |
| `virtual void endFrame(RenderContext*, const ref<Fbo>&)` | 每帧结束时调用 |
| `virtual bool hasWindow() const` | 是否拥有独立窗口 |
| `virtual bool isWindowShown() const` | 窗口是否可见 |
| `virtual void toggleWindow()` | 切换窗口显隐 |
| `virtual void renderUI(Gui* pGui)` | 渲染扩展 UI |
| `virtual bool mouseEvent(const MouseEvent& e)` | 鼠标事件处理 |
| `virtual bool keyboardEvent(const KeyboardEvent& e)` | 键盘事件处理 |
| `virtual bool gamepadEvent(const GamepadEvent& e)` | 手柄事件处理 |
| `virtual void registerScriptBindings(pybind11::module& m)` | 注册 Python 脚本绑定 |
| `virtual std::string getScriptVar() const` | 获取脚本中对应的变量名 |
| `virtual std::string getScript(const std::string& var) const` | 导出配置脚本 |
| `virtual void addGraph(RenderGraph*)` | 添加渲染图时的回调 |
| `virtual void setActiveGraph(RenderGraph*)` | 设置活动渲染图时的回调 |
| `virtual void removeGraph(RenderGraph*)` | 移除渲染图时的回调 |
| `virtual void activeGraphChanged(RenderGraph*, RenderGraph*)` | 活动渲染图切换时的回调 |
| `virtual void onOptionsChange(const Settings::Options&)` | 选项变更时的回调 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpRenderer` | `Renderer*` | 指向宿主渲染器的指针 |
| `mName` | `std::string` | 扩展名称 |

---

### `Renderer`

- **继承**: `SampleApp`（Falcor 框架的示例应用基类）
- **职责**: Mogwai 应用程序的主控制器。管理渲染图集合、场景、扩展插件、编辑器集成、脚本执行、UI 渲染及全部输入事件分发。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `Renderer(const SampleAppConfig&, const Options&)` | 构造函数，初始化应用配置和选项 |
| `void onLoad(RenderContext*)` | 应用加载回调：加载插件、创建扩展、执行启动脚本/场景 |
| `void onFrameRender(RenderContext*, const ref<Fbo>&)` | 每帧渲染入口：处理延迟脚本、编辑器变更、场景更新、图执行、输出 blit |
| `void onResize(uint32_t, uint32_t)` | 窗口尺寸变化时通知所有渲染图和场景 |
| `void onShutdown()` | 关闭时清理编辑器进程、渲染图、管道输出 |
| `void addGraph(const ref<RenderGraph>&)` | 添加渲染图（同名则替换） |
| `void setActiveGraph(const ref<RenderGraph>&)` | 设置活动渲染图 |
| `void removeGraph(const ref<RenderGraph>&)` | 按引用移除渲染图 |
| `void removeGraph(const std::string&)` | 按名称移除渲染图 |
| `ref<RenderGraph> getGraph(const std::string&) const` | 按名称查找渲染图 |
| `void loadScript(const std::filesystem::path&)` | 立即执行 Python 脚本 |
| `void loadScriptDeferred(const std::filesystem::path&)` | 延迟到下帧执行脚本 |
| `void loadScene(std::filesystem::path, SceneBuilder::Flags)` | 加载场景文件 |
| `void unloadScene()` | 卸载当前场景 |
| `void setScene(const ref<Scene>&)` | 设置场景并更新所有渲染图 |
| `void openEditor()` | 启动外部渲染图编辑器 |
| `void saveConfig(const std::filesystem::path&) const` | 将当前配置导出为 Python 脚本 |
| `static void extend(Extension::CreateFunc, const std::string&)` | 注册扩展工厂函数（静态） |
| `static std::string getVersionString()` | 返回 "Mogwai X.Y" 版本字符串 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mOptions` | `Options` | 命令行选项（脚本路径、场景路径、静默模式等） |
| `mpExtensions` | `std::vector<Extension::UniquePtr>` | 已加载的扩展列表 |
| `mGraphs` | `std::vector<GraphData>` | 所有渲染图及其关联数据 |
| `mActiveGraph` | `uint32_t` | 当前活动渲染图的索引 |
| `mpScene` | `ref<Scene>` | 当前加载的场景 |
| `mAppData` | `AppData` | 持久化应用数据（最近文件） |
| `mpSampler` | `ref<Sampler>` | 公共纹理采样器 |
| `mScriptPath` | `std::filesystem::path` | 待延迟加载的脚本路径 |
| `mEditorProcess` | `size_t` | 外部编辑器进程句柄 |
| `mKeyCallback` | `KeyCallback` | 自定义键盘回调 |
| `mSceneUpdateCallback` | `SceneUpdateCallback` | 场景更新回调 |
| `mPipedOutput` | `FILE*` | 管道输出文件句柄（用于 FFmpeg 等） |

---

### `Options`（`Renderer::Options`）

| 成员 | 类型 | 说明 |
|------|------|------|
| `scriptFile` | `std::string` | 启动时执行的脚本文件路径 |
| `deferredLoad` | `bool` | 是否延迟加载脚本 |
| `sceneFile` | `std::string` | 启动时加载的场景文件路径 |
| `silentMode` | `bool` | 静默模式（用于图像测试） |
| `useSceneCache` | `bool` | 是否使用场景缓存 |
| `rebuildSceneCache` | `bool` | 是否重建场景缓存 |

---

### `GraphData`（`Renderer::GraphData`）

| 成员 | 类型 | 说明 |
|------|------|------|
| `pGraph` | `ref<RenderGraph>` | 渲染图实例 |
| `mainOutput` | `std::string` | 主输出名称 |
| `showAllOutputs` | `bool` | 是否显示所有可用输出 |
| `originalOutputs` | `std::vector<std::string>` | 初始标记的输出列表 |
| `debugWindows` | `std::vector<DebugWindow>` | 调试窗口列表 |
| `graphOutputRefs` | `std::unordered_map<std::string, uint32_t>` | 输出引用计数 |
| `sceneUpdates` | `IScene::UpdateFlags` | 累积的场景更新标记 |

---

### `MOGWAI_EXTENSION(Name)` 宏

用于自动注册 Mogwai 扩展。宏展开后会创建一个静态全局对象，在程序启动时自动调用 `Renderer::extend()` 注册扩展的工厂函数。

## 依赖关系

### 本文件引用

- `Falcor.h` — Falcor 框架核心
- `Core/SampleApp.h` — 应用基类
- `Scene/SceneBuilder.h` — 场景构建器
- `RenderGraph/RenderGraph.h` — 渲染图
- `AppData.h` — 持久化数据管理
- `MogwaiSettings.h` — 设置扩展
- `GlobalState.h` — 全局状态
- `Core/AssetResolver.h` — 资产解析器
- `Scene/Importer.h` — 场景导入
- `RenderGraph/RenderGraphImportExport.h` — 渲染图导入导出
- `RenderGraph/RenderPassStandardFlags.h` — 标准渲染通道标志
- `Utils/Scripting/Scripting.h` — 脚本引擎
- `Utils/Timing/TimeReport.h` — 计时报告
- `Utils/Settings/Settings.h` — 设置系统
- `<args.hxx>` — 命令行解析库

### 被以下文件引用

- `Source/Mogwai/MogwaiScripting.cpp` — 脚本绑定
- `Source/Mogwai/MogwaiSettings.h` / `.cpp` — 设置 UI
- `Source/Mogwai/Extensions/Capture/CaptureTrigger.h` — 捕获触发器
- `Source/Mogwai/Extensions/Capture/FrameCapture.h` — 帧捕获
- `Source/Mogwai/Extensions/Profiler/TimingCapture.h` — 计时捕获

## 实现细节

- **扩展注册**: 使用静态 `std::map` 存储扩展工厂函数，在 `onLoad()` 时逐一创建实例。`MogwaiSettings` 作为内建扩展始终第一个加载。
- **渲染图管理**: 支持多个渲染图共存，通过索引切换活动图。添加同名图时自动替换。输出引用计数用于管理调试窗口的额外输出标记。
- **场景更新累积**: 场景更新标记 `sceneUpdates` 会跨帧累积，直到对应渲染图变为活动状态时才消费，确保不会丢失更新。
- **编辑器集成**: 通过启动外部 `RenderGraphEditor` 进程，使用临时文件进行渲染图的导入导出，通过文件监控回调实现实时同步。
- **管道输出**: 支持通过 `popen` 将帧缓冲数据管道传输给 FFmpeg 等外部工具（通过 Settings 选项 `PipedOutput:enable` 和 `PipedOutput:cmd` 控制）。
- **命令行参数**: 支持设备类型选择（D3D12/Vulkan）、GPU 选择、无头模式、脚本/场景加载、着色器调试、光线追踪验证等丰富选项。
- **版本**: 当前版本为 `Mogwai 0.1`。
