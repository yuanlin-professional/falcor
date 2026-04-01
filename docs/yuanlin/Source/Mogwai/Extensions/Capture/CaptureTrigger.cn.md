# CaptureTrigger 源码文档

> 路径: `Source/Mogwai/Extensions/Capture/CaptureTrigger.h`, `Source/Mogwai/Extensions/Capture/CaptureTrigger.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Mogwai / Extensions / Capture

## 功能概述

`CaptureTrigger` 是一个抽象基类，为 Mogwai 的帧范围触发式捕获功能提供通用基础设施。它管理基于帧号的捕获范围（起始帧 + 帧数），在帧循环中自动检测匹配的范围并触发子类的捕获逻辑。同时提供输出目录、基础文件名等公共配置，以及基础 UI 和 Python 脚本绑定。

## 类与接口

### `CaptureTrigger`

- **继承**: `Extension`
- **职责**: 提供基于帧范围的捕获触发机制。子类（如 `FrameCapture`）重写虚函数以实现具体的捕获行为。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `void beginFrame(RenderContext*, const ref<Fbo>&) override final` | 每帧开始时检查当前帧是否匹配某个捕获范围的起始帧，若匹配则调用 `beginRange()` |
| `void endFrame(RenderContext*, const ref<Fbo>&) override final` | 每帧结束时，若处于活动捕获范围内则调用 `triggerFrame()`，到达范围结尾时调用 `endRange()` |
| `void activeGraphChanged(RenderGraph*, RenderGraph*) override` | 活动渲染图切换时，终止当前正在进行的捕获范围 |
| `void registerScriptBindings(pybind11::module& m) override` | 注册 `CaptureTrigger` 基类的 Python 绑定（`reset`、`outputDir`、`baseFilename`） |
| `virtual void beginRange(RenderGraph*, const Range&)` | 子类重写：捕获范围开始时的回调 |
| `virtual void triggerFrame(RenderContext*, RenderGraph*, uint64_t)` | 子类重写：范围内每帧的捕获回调 |
| `virtual void endRange(RenderGraph*, const Range&)` | 子类重写：捕获范围结束时的回调 |
| `void addRange(const RenderGraph*, uint64_t startFrame, uint64_t count)` | 为指定渲染图添加捕获范围，自动检测范围重叠 |
| `void reset(const RenderGraph* pGraph = nullptr)` | 清除指定渲染图或所有渲染图的捕获范围 |
| `void renderBaseUI(Gui::Window& w)` | 渲染基础 UI：文件名输入、输出目录显示和目录选择按钮 |
| `void setOutputDirectory(const std::filesystem::path&)` | 设置输出目录，若可能则转换为相对于运行时目录的相对路径 |
| `void setBaseFilename(const std::string&)` | 设置输出文件的基础文件名 |
| `std::string getScript(const std::string& var) const override` | 导出输出目录和基础文件名的脚本配置 |
| `std::filesystem::path getOutputPath() const` | 获取完整输出目录路径（相对路径基于运行时目录解析） |
| `std::string getOutputNamePrefix(const std::string& output) const` | 生成输出文件名前缀，格式为 `[输出目录]/[基础文件名].[输出名].` |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mGraphRanges` | `std::unordered_map<const RenderGraph*, range_vec>` | 每个渲染图关联的捕获范围列表 |
| `mBaseFilename` | `std::string` | 输出文件的基础文件名，默认为 `"Mogwai"` |
| `mOutputDir` | `std::filesystem::path` | 输出目录，默认为 `"."` |
| `mShowUI` | `bool` | UI 窗口显示状态 |
| `mCurrent` | 匿名结构体 | 当前正在执行的捕获状态：`pGraph`（渲染图）和 `range`（范围） |

#### 类型定义

| 类型 | 定义 | 说明 |
|------|------|------|
| `Range` | `std::pair<uint64_t, uint64_t>` | 捕获范围，first = 起始帧号，second = 帧数 |
| `range_vec` | `std::vector<Range>` | 捕获范围列表 |

## Python 脚本接口

| 方法/属性 | 说明 |
|-----------|------|
| `reset(graph=None)` | 清除捕获范围 |
| `outputDir` | 读写属性：输出目录路径 |
| `baseFilename` | 读写属性：基础文件名 |

## 依赖关系

### 本文件引用

- `../../Mogwai.h` — Renderer 和 Extension 基类
- `Falcor.h` — Falcor 框架核心
- `Utils/Scripting/ScriptWriter.h` — 脚本代码生成

### 被以下文件引用

- `Source/Mogwai/Extensions/Capture/FrameCapture.h` — `FrameCapture` 继承 `CaptureTrigger`

## 实现细节

- **范围重叠检测**: `addRange()` 会检查新范围是否与已有范围重叠，若重叠则抛出异常。完全相同的范围会被静默忽略。`count` 为 0 时表示删除指定起始帧的范围。
- **捕获状态机**: `mCurrent` 结构体跟踪当前正在进行的捕获。`beginFrame()` 检测范围起始，`endFrame()` 在每帧触发并在到达范围末尾时结束。同一时间只能有一个活动捕获范围。
- **路径处理**: 输出目录优先存储为相对于运行时目录的相对路径，减少脚本中的绝对路径依赖。`getOutputPath()` 在需要时将相对路径解析为绝对路径。
- **活动图切换保护**: 当活动渲染图切换时，自动终止当前正在进行的捕获范围，防止状态不一致。
