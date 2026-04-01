# RenderGraphExe 源码文档

> 路径: `Source/Falcor/RenderGraph/RenderGraphExe.h` + `RenderGraphExe.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderGraph (渲染图)

## 功能概述

`RenderGraphExe` 是渲染图的可执行对象，由 `RenderGraphCompiler` 编译生成。它持有已排序的渲染通道执行列表和资源缓存，负责按顺序执行各渲染通道，并提供 UI 渲染、事件处理和资源访问等功能。

## 类与接口

### `RenderGraphExe`

- **继承**: 无
- **职责**: 按编译后的顺序执行渲染通道列表，管理资源缓存，转发 UI 和事件调用。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `void execute(const Context& ctx)` | 按顺序执行所有渲染通道，为每个通道构造 `RenderData` 并调用其 `execute()` |
| `void renderUI(RenderContext*, Gui::Widgets&)` | 渲染所有渲染通道的 UI，每个通道以分组形式展示 |
| `void renderOverlayUI(RenderContext*)` | 渲染所有渲染通道的叠加层 UI |
| `bool onMouseEvent(const MouseEvent&)` | 将鼠标事件分发给所有渲染通道 |
| `bool onKeyEvent(const KeyboardEvent&)` | 将键盘事件分发给所有渲染通道 |
| `void onHotReload(HotReloadFlags)` | 将热重载事件分发给所有渲染通道 |
| `ref<Resource> getResource(const std::string&) const` | 从资源缓存中获取资源 |
| `void setInput(const std::string&, const ref<Resource>&)` | 设置外部输入资源 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mExecutionList` | `std::vector<Pass>` | 渲染通道执行列表（已排序） |
| `mpResourceCache` | `std::unique_ptr<ResourceCache>` | 资源缓存 |

### `Context`

| 成员 | 类型 | 说明 |
|------|------|------|
| `pRenderContext` | `RenderContext*` | 渲染上下文 |
| `passesDictionary` | `Dictionary&` | 通道间通信字典 |
| `defaultTexDims` | `uint2` | 默认纹理尺寸 |
| `defaultTexFormat` | `ResourceFormat` | 默认纹理格式 |

### `Pass`（私有）

| 成员 | 类型 | 说明 |
|------|------|------|
| `name` | `std::string` | 渲染通道名称 |
| `pPass` | `ref<RenderPass>` | 渲染通道引用 |

## 依赖关系

### 本文件引用

- `RenderPass.h` - 渲染通道基类
- `ResourceCache.h` - 资源缓存
- `Utils/Timing/Profiler.h` - 性能分析
- `Utils/Dictionary.h` - 字典
- `Utils/UI/Gui.h` - GUI 工具

### 被以下文件引用

- `RenderGraph.h/cpp` - 渲染图主类
- `RenderGraphCompiler.cpp` - 编译器

## 实现细节

- **执行流程**: `execute()` 遍历执行列表，为每个通道创建 `RenderData`（包含通道名称、资源缓存、字典和默认纹理属性），然后调用通道的 `execute()` 方法。
- **性能分析**: 执行过程使用 `FALCOR_PROFILE` 宏进行性能标记，包括整体执行和每个通道的单独标记。
- **UI 分组**: `renderUI()` 为每个渲染通道创建独立的 UI 分组，使用 `IDScope` 确保不同通道实例的组件 ID 唯一。
- **事件传播**: 鼠标和键盘事件传播给所有渲染通道，使用逻辑或累积处理结果。
- **友元**: `RenderGraphCompiler` 是友元类，负责构建执行列表和设置资源缓存。`Pass` 的构造函数对 `RenderGraphCompiler` 隐藏，强制使用 `insertPass()` 方法。
