# SDFEditor 源码文档

> 路径: `Source/RenderPasses/SDFEditor/SDFEditor.h` + `SDFEditor.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/SDFEditor

## 功能概述

有符号距离场（SDF）编辑器渲染通道。提供交互式的 SDF 几何体编辑功能，允许用户在场景中实时添加、移动、缩放、旋转 SDF 图元（球体、胶囊体、盒体等），支持布尔运算（并集、交集、差集、平滑运算），网格平面辅助、对称平面编辑、撤销/重做操作，以及自动烘焙图元到 SDF 网格。

## 类与接口

### `SDFEditor`

- **继承**: `RenderPass`
- **职责**: SDF 编辑器主类，管理编辑状态、用户输入、2D GUI 覆盖层和 SDF 图元操作

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `SDFEditor(ref<Device>, const Properties&)` | 构造函数 |
| `void execute(RenderContext*, const RenderData&)` | 执行渲染，绑定着色器数据并绘制 GUI 覆盖层 |
| `void renderUI(RenderContext*, Gui::Widgets&)` | 渲染编辑器 UI 面板 |
| `void setScene(RenderContext*, const ref<Scene>&)` | 设置场景，初始化编辑状态 |
| `bool onMouseEvent(const MouseEvent&)` | 处理鼠标事件（点击、拖拽、滚轮） |
| `bool onKeyEvent(const KeyboardEvent&)` | 处理键盘事件（快捷键） |
| `void setup2DGUI()` | 初始化 2D GUI 元素（标记集、选择轮） |
| `void handleActions()` | 处理所有用户操作 |
| `void handleEditMovement()` | 处理编辑图元的移动 |
| `void handleAddPrimitive()` | 处理添加新图元 |
| `bool handlePicking(const float2&, float3&)` | 通过拾取获取 3D 位置 |
| `void handleUndo() / handleRedo()` | 撤销/重做操作 |
| `void bakePrimitives()` | 将非烘焙的图元批量烘焙到 SDF 网格 |
| `void updateEditShapeType()` | 通过选择轮更新编辑形状类型 |
| `void updateEditOperationType()` | 通过选择轮更新编辑操作类型 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpScene` | `ref<Scene>` | 当前场景 |
| `mpGUIPass` | `ref<FullScreenPass>` | 全屏 GUI 渲染通道 |
| `mCurrentEdit` | `CurrentEdit` | 当前正在编辑的图元信息 |
| `mPerformedSDFEdits` | `vector<SDFEdit>` | 已执行的编辑历史 |
| `mUndoneSDFEdits` | `vector<UndoneSDFEdit>` | 已撤销的编辑 |
| `mUI2D` | `UI2D` | 2D GUI 状态（标记集、选择轮、鼠标位置等） |
| `mPrimitiveTransformationEdit` | 匿名结构体 | 图元变换编辑状态 |
| `mInstanceTransformationEdit` | 匿名结构体 | 实例变换编辑状态 |
| `mpPickingInfo` | `ref<Buffer>` | GPU 拾取信息缓冲区 |

### 内部枚举与结构体

| 类型 | 说明 |
|------|------|
| `TransformationState` | 变换状态（None/Translating/Rotating/Scaling） |
| `KeyboardButtonsPressed` | 键盘按键状态追踪 |
| `UI2D` | 2D GUI 状态容器 |
| `CurrentEdit` | 当前编辑的图元和对称图元 |
| `SDFEdit` | 已执行编辑记录 |
| `UndoneSDFEdit` | 已撤销编辑记录 |

## 依赖关系

### 本文件引用

- `Falcor.h`、`Core/Pass/FullScreenPass.h`、`RenderGraph/RenderPass.h`
- `SDFEditorTypes.slang` — 编辑器类型定义
- `Marker2DSet.h` — 2D 标记集
- `SelectionWheel.h` — 选择轮
- `RenderGraph/RenderPassHelpers.h`
- `GUIPass.ps.slang` — GUI 像素着色器

### 被以下文件引用

- 渲染图系统通过插件注册机制加载

## 实现细节

- 支持通过 Tab 键切换编辑/GUI 模式；GUI 模式下显示选择轮用于选择形状和操作类型
- 拾取通过在 GPU 端的 GUI 着色器写入 `SDFPickingInfo` 实现，包括距离、实例 ID 和命中类型
- 撤销/重做通过维护编辑历史列表和撤销列表实现
- 自动烘焙机制在图元数超过 `mPreservedHistoryCount` 时触发，每次烘焙 `mBakePrimitivesBatchSize` 个图元
- 对称编辑通过对称平面实现，图元相对于对称平面镜像后添加第二个图元
