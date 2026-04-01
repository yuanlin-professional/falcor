# RenderGraph 源码文档

> 路径: `Source/Falcor/RenderGraph/RenderGraph.h` + `RenderGraph.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderGraph (渲染图)

## 功能概述

`RenderGraph` 是 Falcor 渲染图系统的核心类，表示一个由渲染通道组成的有向无环图 (DAG)。它负责管理渲染通道的添加/删除、边的连接、图的编译与执行。渲染图通过拓扑排序确定执行顺序，通过资源缓存管理渲染通道之间的资源传递，并支持从 Python 脚本加载和导出。

## 类与接口

### `RenderGraph`

- **继承**: `Object`
- **职责**: 管理渲染通道的 DAG 结构，处理图的编译、执行、输入/输出标记、场景绑定及 UI 事件分发。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<RenderGraph> create(ref<Device>, const std::string& name)` | 创建一个新的渲染图实例 |
| `static ref<RenderGraph> createFromFile(ref<Device>, const std::filesystem::path&)` | 从 Python 脚本文件加载渲染图 |
| `static ref<RenderGraph> createFromString(ref<Device>, const std::string_view)` | 从 Python 脚本字符串加载渲染图 |
| `void setScene(const ref<Scene>&)` | 设置场景，传递给所有渲染通道 |
| `ref<RenderPass> createPass(const std::string& passName, const std::string& passType, const Properties&)` | 创建并添加一个新的渲染通道 |
| `uint32_t addPass(const ref<RenderPass>&, const std::string&)` | 将现有渲染通道添加到图中 |
| `void removePass(const std::string&)` | 移除渲染通道及其所有边 |
| `void updatePass(const std::string&, const Properties&)` | 使用新属性重新创建渲染通道 |
| `const ref<RenderPass>& getPass(const std::string&) const` | 按名称获取渲染通道 |
| `uint32_t addEdge(const std::string& src, const std::string& dst)` | 添加数据依赖边或执行依赖边 |
| `void removeEdge(const std::string& src, const std::string& dst)` | 按名称移除边 |
| `void execute(RenderContext*)` | 编译（如需要）并执行渲染图 |
| `void update(const ref<RenderGraph>&)` | 根据另一个图的拓扑更新当前图 |
| `void setInput(const std::string&, const ref<Resource>&)` | 设置外部输入资源 |
| `void markOutput(const std::string&, TextureChannelFlags)` | 标记渲染通道输出为图输出 |
| `void unmarkOutput(const std::string&)` | 取消标记图输出 |
| `ref<Resource> getOutput(const std::string&)` | 按名称获取输出资源 |
| `void onResize(const Fbo*)` | 交换链大小改变时调用 |
| `bool compile(RenderContext*, std::string& log)` | 编译渲染图 |
| `void renderUI(RenderContext*, Gui::Widgets&)` | 渲染 UI |
| `void onSceneUpdates(RenderContext*, IScene::UpdateFlags)` | 场景更新通知 |
| `bool onMouseEvent(const MouseEvent&)` | 鼠标事件处理 |
| `bool onKeyEvent(const KeyboardEvent&)` | 键盘事件处理 |
| `void onHotReload(HotReloadFlags)` | 热重载回调 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDevice` | `ref<Device>` | GPU 设备引用 |
| `mName` | `std::string` | 渲染图名称 |
| `mpScene` | `ref<Scene>` | 当前场景（可为空） |
| `mpGraph` | `std::unique_ptr<DirectedGraph>` | 底层有向图结构 |
| `mNameToIndex` | `std::unordered_map<std::string, uint32_t>` | 渲染通道名称到节点 ID 的映射 |
| `mEdgeData` | `std::unordered_map<uint32_t, EdgeData>` | 边数据映射 |
| `mNodeData` | `std::unordered_map<uint32_t, NodeData>` | 节点数据映射 |
| `mOutputs` | `std::vector<GraphOut>` | 已标记的图输出列表 |
| `mPassesDictionary` | `Dictionary` | 渲染通道间通信的字典 |
| `mpExe` | `std::unique_ptr<RenderGraphExe>` | 图执行器 |
| `mCompilerDeps` | `RenderGraphCompiler::Dependencies` | 编译器依赖数据 |
| `mRecompile` | `bool` | 是否需要重新编译 |

### 内部结构体

| 结构体 | 说明 |
|--------|------|
| `EdgeData` | 存储边的源字段和目标字段名称 |
| `NodeData` | 存储节点名称和对应的渲染通道指针 |
| `GraphOut` | 存储图输出信息（节点 ID、字段名、颜色通道掩码） |

## 依赖关系

### 本文件引用

- `RenderPass.h` - 渲染通道基类
- `RenderGraphExe.h` - 图执行器
- `RenderGraphCompiler.h` - 图编译器
- `RenderGraphIR.h` - 中间表示
- `RenderGraphImportExport.h` - 导入导出
- `Core/Object.h` - 对象基类
- `Utils/Algorithm/DirectedGraph.h` - 有向图算法
- `Scene/Scene.h` - 场景管理
- `Utils/Scripting/Scripting.h` - Python 脚本支持

### 被以下文件引用

- `RenderGraphUI.h/cpp` - 渲染图 UI
- `RenderGraphImportExport.h/cpp` - 导入导出功能
- `RenderGraphCompiler.h/cpp` - 编译器

## 实现细节

- **图结构**: 使用 `DirectedGraph` 实现 DAG，节点仅存储 ID，实际数据保存在 `mNodeData` 和 `mEdgeData` 中。
- **边的类型**: 支持两种边：数据依赖边（格式为 `passName.resourceName`）和执行依赖边（仅 `passName`）。数据依赖边在不影响输出时可被优化移除，执行依赖边始终执行。
- **环检测**: 添加边时使用 `DirectedGraphPathDetector` 检测是否会形成环路。
- **延迟编译**: 图的拓扑变更（添加/移除通道或边、场景变更、窗口大小变更）会将 `mRecompile` 设为 true，实际编译推迟到 `execute()` 调用时。
- **Python 绑定**: 通过 `FALCOR_SCRIPT_BINDING` 宏注册了 `RenderPass` 和 `RenderGraph` 的 Python 绑定，支持脚本化的图构建。
- **通配符输出**: `markOutput("*")` 可标记所有可用输出。
