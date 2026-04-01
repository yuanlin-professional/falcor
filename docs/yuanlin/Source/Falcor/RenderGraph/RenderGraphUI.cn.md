# RenderGraphUI 源码文档

> 路径: `Source/Falcor/RenderGraph/RenderGraphUI.h` + `RenderGraphUI.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderGraph (渲染图)

## 功能概述

`RenderGraphUI` 和 `RenderPassUI` 提供渲染图编辑器的可视化界面功能。`RenderGraphUI` 管理整个渲染图的节点图编辑器，支持节点的拖拽、连接、添加/删除，以及图输出的管理。`RenderPassUI` 负责单个渲染通道节点的 Pin（引脚）绘制和交互。

## 类与接口

### `RenderPassUI`

- **继承**: 无
- **职责**: 管理单个渲染通道在节点图编辑器中的引脚（输入/输出端口）显示。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `void addUIPin(const std::string& fieldName, uint32_t guiPinID, bool isInput, ...)` | 为渲染通道添加一个 UI 引脚 |
| `void renderPinUI(const std::string& passName, RenderGraphUI*, uint32_t index, bool input)` | 渲染指定引脚的 UI |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mInputPins` | `std::vector<PinUI>` | 输入引脚列表 |
| `mOutputPins` | `std::vector<PinUI>` | 输出引脚列表 |
| `mGuiNodeID` | `uint32_t` | GUI 节点 ID |
| `mReflection` | `RenderPassReflection` | 渲染通道反射数据 |

### `RenderGraphUI`

- **继承**: 无
- **职责**: 管理渲染图节点编辑器的完整 UI，包括节点布局、连接管理和变更脚本生成。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `RenderGraphUI(const ref<RenderGraph>&, const std::string& graphName)` | 构造函数 |
| `void renderUI(RenderContext*, Gui*)` | 渲染完整的图编辑器 UI |
| `void reset()` | 清除 UI 以重建节点图 |
| `void setToRebuild()` | 标记需要重建显示数据 |
| `void writeUpdateScriptToFile(RenderContext*, const std::filesystem::path&, float)` | 将变更写出为更新脚本 |
| `bool addLink(const std::string& srcPass, const std::string& dstPass, ...)` | 在图中添加连接 |
| `void removeEdge(...)` | 移除边 |
| `void removeRenderPass(const std::string&)` | 移除渲染通道 |
| `void addOutput(const std::string&)` | 添加图输出 |
| `void removeOutput(const std::string& outputPass, const std::string& outputField)` | 移除图输出 |
| `void addRenderPass(const std::string& name, const std::string& nodeTypeName)` | 添加新的渲染通道节点 |
| `void setRecordUpdates(bool)` | 切换是否记录增量变更 |
| `void updateGraph(RenderContext*)` | 根据脚本更新图 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpRenderGraph` | `ref<RenderGraph>` | 关联的渲染图 |
| `mpIr` | `std::shared_ptr<RenderGraphIR>` | 中间表示生成器 |
| `mRenderPassUI` | `std::unordered_map<std::string, RenderPassUI>` | 各通道的 UI 数据 |
| `mpNodeGraphEditor` | `std::shared_ptr<NodeGraphEditorGui>` | 底层节点图编辑器 |
| `mUpdateCommands` | `std::string` | 累积的更新命令 |
| `mRecordUpdates` | `bool` | 是否记录变更 |
| `mRebuildDisplayData` | `bool` | 是否需要重建显示数据 |

## 依赖关系

### 本文件引用

- `RenderGraph.h` - 渲染图
- `RenderGraphIR.h` - 中间表示
- `RenderPassReflection.h` - 渲染通道反射
- `Utils/Algorithm/DirectedGraphTraversal.h` - 图遍历

### 被以下文件引用

- `RenderGraph.h` - 声明为友元类

## 实现细节

- **节点图编辑器**: 使用内部 `NodeGraphEditorGui` 类封装底层节点图 GUI 库（如 ImGui 节点编辑器）。
- **显示数据重建**: 图拓扑变更后设置 `mRebuildDisplayData = true`，下次 `renderUI()` 时重新遍历图结构并更新所有 UI 数据。
- **引脚连接更新**: `updatePins()` 方法根据图边数据更新所有引脚的连接状态和图输出标记。
- **变更记录**: 当 `mRecordUpdates = true` 时，所有图编辑操作（添加/移除通道、边、输出）都会同时生成 `RenderGraphIR` 命令，用于实时预览和脚本导出。
- **拖放支持**: 支持从渲染通道类型列表拖放创建新节点。
- **自动解析警告**: 当连接可能触发自动 MSAA 解析时，显示弹出提示。
