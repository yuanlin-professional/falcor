# FullScreenPass 源码文档

> 路径: `Source/Falcor/Core/Pass/FullScreenPass.h` + `FullScreenPass.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Core/Pass

## 功能概述

`FullScreenPass` 实现了全屏渲染通道，用于在整个屏幕上执行像素着色器操作。常用于后处理效果、图像合成、Tone Mapping 等场景。该类内部管理了全屏四边形的顶点缓冲区和顶点数组对象（VAO），并通过共享缓存机制避免重复创建。支持多视口（multi-projection）渲染，通过可选的几何着色器将图元广播到多个渲染目标层。

## 类与接口

### `FullScreenPass`

- **继承**: `BaseGraphicsPass`
- **职责**: 管理全屏四边形的绘制资源，执行全屏像素着色器

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<FullScreenPass> create(ref<Device>, const std::filesystem::path& path, const DefineList& = DefineList(), uint32_t viewportMask = 0)` | 从像素着色器文件路径创建全屏通道。`path` 中需包含名为 `main()` 的像素着色器入口 |
| `static ref<FullScreenPass> create(ref<Device>, const ProgramDesc&, const DefineList& = DefineList(), uint32_t viewportMask = 0)` | 从程序描述创建全屏通道。自动附加默认顶点着色器 |
| `virtual void execute(RenderContext* pRenderContext, const ref<Fbo>& pFbo, bool autoSetVpSc = true) const` | 执行全屏渲染。`autoSetVpSc` 控制是否自动将视口和裁剪矩形设为 FBO 尺寸 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpSharedData` | `std::shared_ptr<SharedData>` | 共享的顶点缓冲区和 VAO 数据 |

### `FullScreenPass::SharedData`（内部结构体）

| 成员 | 类型 | 说明 |
|------|------|------|
| `pVertexBuffer` | `ref<Buffer>` | 全屏四边形的顶点缓冲区 |
| `pVao` | `ref<Vao>` | 顶点数组对象 |
| `objectCount` | `uint64_t` | 引用计数 |

### `Vertex`（匿名命名空间内部结构体）

| 成员 | 类型 | 说明 |
|------|------|------|
| `screenPos` | `float2` | 屏幕空间坐标 |
| `texCoord` | `float2` | 纹理坐标 |

## 依赖关系

### 本文件引用

- `BaseGraphicsPass.h` — 基类
- `Core/Macros.h` — Falcor 宏定义
- `Core/Program/Program.h` — 着色器程序
- `Core/API/RenderContext.h` — 渲染上下文（实现文件）
- `Utils/SharedCache.h` — 共享缓存工具（实现文件）
- `Core/Pass/FullScreenPass.vs.slang` — 默认顶点着色器
- `Core/Pass/FullScreenPass.gs.slang` — 多视口几何着色器（可选）

### 被以下文件引用

- 各类后处理渲染通道（Tone Mapping、模糊、FXAA 等）

## 实现细节

- **全屏四边形**: 使用 4 个顶点的 TriangleStrip 覆盖整个 NDC 空间 `[-1, 1]`，对应纹理坐标 `[0, 1]`。
- **共享缓存**: 通过 `SharedCache<SharedData, Device*>` 实现同一设备上多个 `FullScreenPass` 实例共享同一份顶点缓冲区和 VAO，节省显存。
- **深度测试**: 构造时自动禁用深度测试（`setDepthEnabled(false)`），因为全屏通道通常不需要深度比较。
- **多视口支持**: 当 `viewportMask` 非零时，自动添加几何着色器（`FullScreenPass.gs.slang`），通过 `_VIEWPORT_MASK` 和 `_OUTPUT_VERTEX_COUNT` 宏定义控制输出到哪些渲染目标层。
- **默认顶点着色器**: 若程序描述中未指定顶点着色器入口，自动使用 `FullScreenPass.vs.slang`。
