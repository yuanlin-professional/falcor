# RasterPass 源码文档

> 路径: `Source/Falcor/Core/Pass/RasterPass.h` + `RasterPass.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/Pass

## 功能概述

`RasterPass` 封装了通用的光栅化渲染通道，支持顺序绘制（draw）和索引绘制（drawIndexed）两种模式。与 `FullScreenPass` 不同，`RasterPass` 适用于需要自定义几何体的渲染场景，如场景渲染、阴影贴图生成等。

## 类与接口

### `RasterPass`

- **继承**: `BaseGraphicsPass`
- **职责**: 提供通用的光栅化绘制接口，将管线状态和程序变量绑定到渲染上下文后执行绘制调用

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<RasterPass> create(ref<Device>, const std::filesystem::path& path, const std::string& vsEntry, const std::string& psEntry, const DefineList& = DefineList())` | 从着色器文件路径创建光栅化通道，需指定顶点着色器和像素着色器入口点 |
| `static ref<RasterPass> create(ref<Device>, const ProgramDesc& desc, const DefineList& = DefineList())` | 从程序描述创建光栅化通道 |
| `void draw(RenderContext* pRenderContext, uint32_t vertexCount, uint32_t startVertexLocation)` | 顺序绘制调用。按顶点顺序绘制指定数量的顶点 |
| `void drawIndexed(RenderContext* pRenderContext, uint32_t indexCount, uint32_t startIndexLocation, int32_t baseVertexLocation)` | 索引绘制调用。通过索引缓冲区查找顶点进行绘制 |

## 依赖关系

### 本文件引用

- `BaseGraphicsPass.h` — 基类
- `Core/Macros.h` — Falcor 宏定义
- `Core/Program/Program.h` — 着色器程序
- `Core/API/RenderContext.h` — 渲染上下文（实现文件）

### 被以下文件引用

- 各类需要自定义几何体光栅化的渲染通道（场景渲染、阴影生成等）

## 实现细节

- 构造函数直接委托给基类 `BaseGraphicsPass`，无额外初始化逻辑。
- 从文件路径创建时，自动将着色器文件注册为库，并设置顶点着色器（`vsEntry`）和像素着色器（`psEntry`）入口点。
- `draw` 和 `drawIndexed` 方法将内部持有的管线状态和程序变量传递给 `RenderContext` 的对应方法，实现绘制调用的封装。
- 与 `FullScreenPass` 相比，`RasterPass` 不管理顶点缓冲区和 VAO，这些资源由调用方通过 `getState()` 手动设置。
