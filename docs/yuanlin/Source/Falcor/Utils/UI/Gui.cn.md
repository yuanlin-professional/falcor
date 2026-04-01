# Gui 源码文档

> 路径: `Source/Falcor/Utils/UI/Gui.h` + `Source/Falcor/Utils/UI/Gui.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Utils/UI

## 功能概述

Gui 类是 Falcor 框架对 ImGui 库的高层封装，提供了一套完整的即时模式 GUI 系统。它支持窗口、分组、菜单栏、各类输入控件（下拉框、滑块、文本框、颜色选择器等）以及图片显示和光谱编辑器等高级功能。该类负责 ImGui 的初始化、渲染管线搭建、字体管理以及鼠标/键盘事件的传递。

## 类与接口

### `Gui`

- **继承**: 无
- **职责**: 封装 ImGui，提供 Falcor 风格的 GUI 控件接口、渲染管线管理和输入事件处理

#### 枚举类型

| 枚举 | 说明 |
|------|------|
| `TextFlags` | 文本控件标志，`FitWindow` 表示自适应窗口宽度并隐藏标签 |
| `WindowFlags` | 窗口标志，支持标题栏、可移动、自动聚焦、关闭按钮、禁止缩放、自动缩放等 |
| `WidgetFlags` | 控件标志，`SameLine` 同行显示，`Inactive` 禁用编辑 |

#### 内部结构体与类型别名

| 类型 | 说明 |
|------|------|
| `DropdownValue` | 下拉菜单选项，含 `value`（唯一索引）和 `label`（显示文本） |
| `DropdownList` | `std::vector<DropdownValue>`，下拉选项列表 |
| `RadioButton` | 单选按钮项，含 ID、标签和是否同行显示 |
| `RadioButtonGroup` | `std::vector<RadioButton>`，单选按钮组 |
| `GraphCallback` | 图表回调函数指针 `float(*)(void*, int32_t)` |

### `Gui::Widgets`

- **继承**: 无（基类）
- **职责**: 提供所有基础 GUI 控件的接口，是 Group 和 Window 的基类

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `Group group(const std::string& label, bool beginExpanded = false)` | 创建可折叠分组 |
| `void indent(float i)` | 缩进下一个控件 |
| `void separator(uint32_t count = 1)` | 添加分隔线 |
| `void dummy(const char label[], const float2& size, bool sameLine = false)` | 添加空白占位 |
| `void rect(const float2& size, const float4& color, bool filled, bool sameLine)` | 绘制矩形 |
| `bool dropdown(const char label[], const DropdownList& values, uint32_t& var, bool sameLine)` | 下拉菜单 |
| `template<typename T> bool dropdown(const char label[], T& var, bool sameLine)` | 基于 FALCOR_ENUM_INFO 的枚举下拉菜单 |
| `bool button(const char label[], bool sameLine)` | 按钮 |
| `bool radioButtons(const RadioButtonGroup& buttons, uint32_t& activeID)` | 单选按钮组 |
| `bool direction(const char label[], float3& direction)` | 方向控件 |
| `template<typename T> bool checkbox(const char label[], T& var, bool sameLine)` | 复选框 |
| `bool dragDropSource(...)` / `bool dragDropDest(...)` | 拖放源/目标 |
| `void text(...)` / `void textWrapped(...)` / `bool textbox(...)` | 文本显示与输入 |
| `void tooltip(const std::string& text, bool sameLine)` | 工具提示 |
| `bool rgbColor(...)` / `bool rgbaColor(...)` | RGB/RGBA 颜色选择器 |
| `void image(...)` / `bool imageButton(...)` | 图片显示与图片按钮 |
| `template<typename T> bool var(...)` | 标量/向量数值输入 |
| `template<typename T> bool slider(...)` | 标量/向量滑块 |
| `template<typename MatrixType> bool matrix(...)` | 矩阵编辑控件 |
| `void graph(...)` | 函数图表 |
| `template<typename T> bool spectrum(...)` | 光谱编辑器 |

### `Gui::Group`

- **继承**: `Gui::Widgets`
- **职责**: 可折叠的控件分组，对应 ImGui 的 CollapsingHeader/TreeNode

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `Group(Gui* pGui, const std::string& label, bool beginExpanded)` | 构造，创建分组 |
| `bool open() const` | 检查分组是否展开 |
| `operator bool() const` | 布尔转换，等同于 `open()` |
| `void release()` | 结束分组块 |

### `Gui::Window`

- **继承**: `Gui::Widgets`
- **职责**: 独立的 GUI 窗口容器

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `Window(Gui* pGui, const char* name, uint2 size, uint2 pos, WindowFlags flags)` | 创建窗口 |
| `Window(Gui* pGui, const char* name, bool& open, ...)` | 创建带关闭状态的窗口 |
| `void release()` | 结束窗口 |
| `void columns(uint32_t numColumns)` | 创建列布局 |
| `void nextColumn()` | 切换到下一列 |
| `void windowPos(uint32_t x, uint32_t y)` | 设置窗口位置 |
| `void windowSize(uint32_t width, uint32_t height)` | 设置窗口大小 |

### `Gui::Menu`

- **继承**: 无
- **职责**: 菜单容器，支持嵌套下拉菜单

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `Menu(Gui* pGui, const char* name)` | 创建菜单 |
| `Dropdown dropdown(const std::string& label)` | 创建下拉子菜单 |
| `bool item(const std::string& label)` | 添加菜单项 |

### `Gui::Menu::Dropdown`

- **继承**: 无
- **职责**: 下拉菜单容器

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `bool item(const std::string& label, bool& var, const std::string& shortcut)` | 带选中状态的菜单项 |
| `bool item(const std::string& label, const std::string& shortcut)` | 普通菜单项 |
| `void separator()` | 分隔线 |
| `Menu menu(const char* name)` | 嵌套子菜单 |

### `Gui::MainMenu`

- **继承**: `Gui::Menu`
- **职责**: 应用程序主菜单栏

### `IDScope`

- **继承**: 无
- **职责**: ImGui ID 作用域的 RAII 辅助类，构造时 `PushID`，析构时 `PopID`

### `GuiImpl`（cpp 内部）

- **继承**: 无
- **职责**: Gui 的内部实现类（Pimpl 模式），管理 ImGui 上下文、渲染管线状态、VAO 轮转、字体映射等

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpContext` | `ImGuiContext*` | ImGui 上下文 |
| `mpPipelineState` | `ref<GraphicsState>` | 图形管线状态 |
| `mpProgram` | `ref<Program>` | GUI 着色器程序 |
| `mpProgramVars` | `ref<ProgramVars>` | 着色器变量 |
| `mpVaos` | `ref<Vao>[kVaoCount]` | 轮转使用的 VAO（避免 GPU 停顿） |
| `mFontMap` | `unordered_map<string, ImFont*>` | 字体映射表 |
| `mLoadedImages` | `map<path, ref<Texture>>` | 已加载图片缓存 |

#### 主要方法

| 方法签名 | 说明 |
|----------|------|
| `Gui(ref<Device>, uint32_t width, uint32_t height, float scaleFactor)` | 构造，初始化 ImGui 和渲染管线 |
| `void beginFrame()` | 开始新一帧的 GUI |
| `void render(RenderContext*, const ref<Fbo>&, float elapsedTime)` | 渲染 GUI 到指定 FBO |
| `void onWindowResize(uint32_t width, uint32_t height)` | 处理窗口大小变化 |
| `bool onMouseEvent(const MouseEvent&)` | 处理鼠标事件 |
| `bool onKeyboardEvent(const KeyboardEvent&)` | 处理键盘事件 |
| `void addFont(const std::string& name, const std::filesystem::path& path)` | 添加字体 |
| `void setActiveFont(const std::string& font)` | 设置当前活动字体 |
| `static float4 pickUniqueColor(const std::string& key)` | 基于字符串生成唯一颜色 |

## 依赖关系

### 本文件引用

- `Core/Macros.h` — 宏定义
- `Core/Enum.h` — 枚举工具（`has_enum_info_v`, `EnumInfo`）
- `Core/API/Texture.h` — 纹理
- `Core/API/FBO.h` — 帧缓冲对象
- `Core/API/VAO.h` — 顶点数组对象
- `Core/API/VertexLayout.h` — 顶点布局
- `Core/API/RenderContext.h` — 渲染上下文
- `Core/State/GraphicsState.h` — 图形管线状态
- `Core/Program/Program.h` / `ProgramVars.h` — 着色器程序与变量
- `Utils/Math/Vector.h` — 数学向量类型
- `Utils/Color/SampledSpectrum.h` — 采样光谱
- `Utils/Logger.h` — 日志
- `Utils/StringUtils.h` — 字符串工具
- `Utils/UI/InputTypes.h` — 输入事件类型
- `Utils/UI/SpectrumUI.h` — 光谱 UI 组件
- `Utils/UI/Gui.slang` — GUI 渲染着色器
- `imgui.h` — ImGui 库

### 被以下文件引用

- `Utils/UI/SpectrumUI.h` — 使用 `Gui::Widgets` 和 `Gui::Group`
- `Utils/UI/PythonUI.h` — 通过 ImGui 直接调用（共享 ImGui 上下文）
- Falcor 框架中大量渲染 Pass 和应用程序类通过 `Gui::Widgets` 提供 UI

## 实现细节

- 采用 Pimpl（指针到实现）模式，`GuiImpl` 隐藏了所有 ImGui 细节
- 使用 3 个轮转 VAO 避免 GPU 停顿（triple buffering）
- 每帧通过 `beginFrame()` 开始新帧，`render()` 将 ImGui 绘制数据转换为 GPU 绘制调用
- 鼠标/键盘事件通过缓存机制传递给 ImGui，防止事件丢失
- 下拉菜单值通过 `mDropDownValues` 缓存映射，支持外部变量与 ImGui 内部索引的同步
- 枚举类型的下拉菜单通过 `FALCOR_ENUM_INFO` 和模板特化自动生成选项列表
- 图片资源通过 `mLoadedImages` 缓存，避免重复加载
- `pickUniqueColor` 使用字符串哈希生成确定性的唯一颜色
