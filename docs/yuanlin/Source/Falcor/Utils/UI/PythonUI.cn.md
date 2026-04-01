# PythonUI 源码文档

> 路径: `Source/Falcor/Utils/UI/PythonUI.h` + `Source/Falcor/Utils/UI/PythonUI.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/UI

## 功能概述

PythonUI 模块为 Falcor 提供了一套可从 Python 脚本中创建和操控的 GUI 控件体系。基于 ImGui 实现，通过 pybind11 导出到 Python，支持窗口、分组、文本、按钮、复选框、组合框、进度条、拖拽控件和滑块等多种控件类型。控件采用父子树形结构组织，自动管理生命周期。

## 类与接口

### `python_ui::Widget`（头文件中定义）

- **继承**: `Object`（Falcor 引用计数基类）
- **职责**: 所有 Python UI 控件的基类，管理父子关系、可见性、启用状态和递归渲染

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `Widget(Widget* parent)` | 构造时自动注册为父控件的子节点 |
| `Widget* get_parent()` / `void set_parent(Widget*)` | 获取/设置父控件 |
| `const std::vector<ref<Widget>>& get_children() const` | 获取子控件列表 |
| `bool get_visible()` / `void set_visible(bool)` | 可见性控制 |
| `bool get_enabled()` / `void set_enabled(bool)` | 启用/禁用控制 |
| `virtual void render()` | 递归渲染所有可见子控件 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `m_parent` | `Widget*` | 父控件（非拥有指针） |
| `m_children` | `std::vector<ref<Widget>>` | 子控件列表（拥有引用） |
| `m_visible` | `bool` | 是否可见，默认 true |
| `m_enabled` | `bool` | 是否启用，默认 true |

### `python_ui::Screen`（头文件中定义）

- **继承**: `Widget`
- **职责**: 根控件，代表整个屏幕，作为 Window 控件的父节点

### `python_ui::Window`（cpp 中定义）

- **继承**: `Widget`
- **职责**: 可拖拽、可缩放的独立窗口

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `Window(Widget* parent, string_view title, float2 position, float2 size)` | 创建窗口 |
| `void show()` / `void close()` | 显示/隐藏窗口 |
| `get/set_title`、`get/set_position`、`get/set_size` | 属性访问 |

### `python_ui::Group`（cpp 中定义）

- **继承**: `Widget`
- **职责**: 可折叠分组，嵌套时自动切换 TreeNode/CollapsingHeader 样式

### `python_ui::Text`（cpp 中定义）

- **继承**: `Widget`
- **职责**: 静态文本显示

### `python_ui::ProgressBar`（cpp 中定义）

- **继承**: `Widget`
- **职责**: 进度条控件

### `python_ui::Button`（cpp 中定义）

- **继承**: `Widget`
- **职责**: 按钮控件，支持点击回调

### `python_ui::Property`（cpp 中定义）

- **继承**: `Widget`
- **职责**: 带标签和值变化回调的属性控件基类

### `python_ui::Checkbox`（cpp 中定义）

- **继承**: `Property`
- **职责**: 复选框控件

### `python_ui::Combobox`（cpp 中定义）

- **继承**: `Property`
- **职责**: 组合框/下拉选择控件

### `python_ui::Drag<T>`（cpp 中定义，模板类）

- **继承**: `Property`
- **职责**: 拖拽数值输入控件，支持 float/float2/float3/float4/int/int2/int3/int4

#### 类型别名

`DragFloat`、`DragFloat2`、`DragFloat3`、`DragFloat4`、`DragInt`、`DragInt2`、`DragInt3`、`DragInt4`

### `python_ui::Slider<T>`（cpp 中定义，模板类）

- **继承**: `Property`
- **职责**: 滑块控件，支持 float/float2/float3/float4/int/int2/int3/int4

#### 类型别名

`SliderFloat`、`SliderFloat2`、`SliderFloat3`、`SliderFloat4`、`SliderInt`、`SliderInt2`、`SliderInt3`、`SliderInt4`

### 辅助类（cpp 内部）

| 类 | 说明 |
|----|------|
| `ScopedID` | ImGui ID 作用域 RAII 辅助，使用控件指针作为唯一 ID |
| `ScopedDisable` | ImGui 禁用状态作用域 RAII 辅助 |

### 枚举

| 枚举 | 说明 |
|------|------|
| `SliderFlags` | 滑块标志：`None`、`AlwaysClamp`（始终限制范围）、`Logarithmic`（对数刻度）、`NoRoundToFormat`（不四舍五入）、`NoInput`（禁止键盘输入） |

### `SliderTraits<T>`（模板特化）

为每种数值类型提供 ImGui 调用所需的元信息：标量类型、是否浮点、分量数、默认格式字符串。

## 依赖关系

### 本文件引用

- `Core/Object.h` — `Object` 基类和 `ref<>` 智能指针
- `Utils/Scripting/ScriptBindings.h` — `FALCOR_SCRIPT_BINDING` 宏
- `Utils/Math/Vector.h` — 向量类型
- `imgui.h` — ImGui 库
- `pybind11/stl_bind.h` — pybind11 STL 容器绑定

### 被以下文件引用

- Python 脚本通过 `falcor.ui` 模块访问所有控件
- Falcor 应用程序框架渲染 Screen 控件的 `render()` 方法

## 实现细节

- 所有具体控件类（Window、Group、Text 等）仅在 `.cpp` 中定义，头文件只暴露 `Widget` 和 `Screen` 基类
- 控件采用树形父子结构：构造时自动将自身添加到父控件的子列表中
- 每个控件在渲染时使用 `ScopedID` 推入唯一 ID（使用 `this` 指针），避免 ImGui ID 冲突
- `ScopedDisable` 用于在控件被禁用时调用 `ImGui::BeginDisabled()`/`EndDisabled()`
- `Group` 控件智能检测嵌套层级：顶层使用 `CollapsingHeader`，嵌套层使用 `TreeNodeEx`
- `Window` 控件跟踪 ImGui 窗口的实际位置和大小，支持 Python 端读取
- pybind11 绑定通过 `FALCOR_SCRIPT_BINDING(python_ui)` 宏注册到 `falcor.ui` 子模块
- `bind_drag<T>` 和 `bind_slider<T>` 模板函数避免了重复的绑定代码
