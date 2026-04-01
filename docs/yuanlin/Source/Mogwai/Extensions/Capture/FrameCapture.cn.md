# FrameCapture 源码文档

> 路径: `Source/Mogwai/Extensions/Capture/FrameCapture.h`, `Source/Mogwai/Extensions/Capture/FrameCapture.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Mogwai / Extensions / Capture

## 功能概述

`FrameCapture` 是 Mogwai 的帧捕获扩展，继承自 `CaptureTrigger`，用于将渲染图的输出保存为图像文件。支持按帧号列表自动捕获或手动捕获当前帧，可选择仅保存标记输出或所有可用输出。支持多通道分离输出（R/G/B/A/RGB/RGBA），自动根据资源格式选择合适的输出图像格式。

## 类与接口

### `FrameCapture`

- **继承**: `CaptureTrigger`
- **职责**: 在指定帧号将渲染图输出保存为图像文件。通过 `MOGWAI_EXTENSION` 宏自动注册为 Mogwai 扩展。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static UniquePtr create(Renderer* pRenderer)` | 工厂方法，创建 `FrameCapture` 实例并初始化 `ImageProcessing` |
| `void renderUI(Gui* pGui) override` | 渲染帧捕获 UI：基础设置、"捕获所有输出"复选框、"捕获当前帧"按钮 |
| `void registerScriptBindings(pybind11::module& m) override` | 注册 `FrameCapture` 的 Python 绑定 |
| `std::string getScriptVar() const override` | 返回脚本变量名 `"frameCapture"` |
| `std::string getScript(const std::string& var) const override` | 导出配置脚本，包括基础配置和每个渲染图的帧号列表 |
| `void triggerFrame(RenderContext*, RenderGraph*, uint64_t) override` | 触发帧捕获：可选标记所有输出，然后逐一保存每个输出 |
| `void capture()` | 手动捕获当前帧（供 UI 按钮和脚本调用） |
| `void addFrames(const RenderGraph*, const uint64_vec&)` | 为渲染图添加需要捕获的帧号列表 |
| `void addFrames(const std::string&, const uint64_vec&)` | 按渲染图名称添加帧号列表 |
| `std::string graphFramesStr(const RenderGraph*)` | 获取渲染图已设置帧号的字符串表示 |
| `void captureOutput(RenderContext*, RenderGraph*, uint32_t)` | 捕获单个输出：根据通道掩码分离通道，确定输出格式，写入文件 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mCaptureAllOutputs` | `bool` | 是否捕获所有输出（包括未标记的） |
| `mpImageProcessing` | `std::unique_ptr<ImageProcessing>` | 图像处理工具，用于通道提取 |

## Python 脚本接口

| 方法/属性 | 说明 |
|-----------|------|
| `addFrames(graph, frames)` | 为渲染图对象添加帧号列表 |
| `addFrames(name, frames)` | 按渲染图名称添加帧号列表 |
| `print(graph)` | 打印指定渲染图的帧号列表 |
| `print()` | 打印所有渲染图的帧号列表 |
| `capture()` | 立即捕获当前帧 |
| `ui` | 读写属性：UI 窗口显隐 |
| `captureAllOutputs` | 读写属性：是否捕获所有输出 |

继承自 `CaptureTrigger` 的接口（`reset`、`outputDir`、`baseFilename`）同样可用。

## 输出文件命名

文件命名格式为：`[输出目录]/[基础文件名].[输出名].[帧号][.通道后缀].[扩展名]`

通道后缀规则：
| 通道掩码 | 后缀 |
|----------|------|
| Red | `.R` |
| Green | `.G` |
| Blue | `.B` |
| Alpha | `.A` |
| RGB | （无后缀） |
| RGBA | `.RGBA` |

## 依赖关系

### 本文件引用

- `../../Mogwai.h` — Renderer 和 Extension 基类
- `CaptureTrigger.h` — 捕获触发器基类
- `Utils/Image/ImageProcessing.h` — 图像处理（通道提取）
- `Falcor.h` — Falcor 框架核心
- `Utils/Scripting/ScriptWriter.h` — 脚本代码生成

### 被以下文件引用

- 通过 `MOGWAI_EXTENSION(FrameCapture)` 宏自动注册，被渲染器在启动时发现和加载

## 实现细节

- **自动注册**: 通过 `MOGWAI_EXTENSION(FrameCapture)` 宏，在静态初始化阶段自动调用 `Renderer::extend()` 注册工厂函数。
- **全输出捕获**: 当 `mCaptureAllOutputs` 为 true 时，先标记所有未标记输出，重新执行渲染图，捕获完成后恢复原始标记状态并重新编译。
- **通道分离**: 单通道输出时，使用 `ImageProcessing::copyColorChannel()` 将指定通道复制到独立纹理中。支持 Unorm、Snorm、Uint、Sint、Float 等格式的 8/16/32 位单通道输出格式。
- **SRGB 精度警告**: 从 SRGB 格式中提取单个 RGB 通道时，转换到非 SRGB 单通道格式可能导致精度损失，会输出警告。
- **帧号映射**: 内部将帧号列表转换为帧范围（`count = 1` 的 `Range`），复用 `CaptureTrigger` 的范围管理机制。
- **文件格式自动选择**: 通过 `Bitmap::getFileExtFromResourceFormat()` 根据资源格式自动确定输出图像的文件扩展名和格式。
