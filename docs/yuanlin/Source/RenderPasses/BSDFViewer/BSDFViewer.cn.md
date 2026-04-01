# BSDFViewer 源码文档

> 路径: `Source/RenderPasses/BSDFViewer/BSDFViewer.h` + `Source/RenderPasses/BSDFViewer/BSDFViewer.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/BSDFViewer

## 功能概述

BSDFViewer 是一个双向散射分布函数检查工具渲染通道。它提供两种查看模式：材质模式（渲染带光照的单位球）和切片模式（显示双向散射分布函数的 2D 切片视图）。支持交互式选择材质、配置光照（方向光/全向光/环境贴图）、切换相机模式（正交/透视），以及像素级数据回读。

## 类与接口

### `BSDFViewer`

- **继承**: `RenderPass`
- **职责**: 提供双向散射分布函数交互式可视化和检查工具

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `BSDFViewer(ref<Device>, const Properties&)` | 构造函数，创建采样生成器、像素调试工具和 GPU 围栏 |
| `Properties getProperties() const override` | 序列化材质 ID、查看模式、环境贴图等配置 |
| `RenderPassReflection reflect(const CompileData&) override` | 声明单个 `output` 输出缓冲区（RGBA32Float） |
| `void compile(RenderContext*, const CompileData&) override` | 计算居中正方形视口的偏移和缩放 |
| `void execute(RenderContext*, const RenderData&) override` | 设置着色器定义、绑定资源、执行计算通道、回读像素数据 |
| `void renderUI(Gui::Widgets&) override` | 丰富的 UI：模式、材质、双向散射分布函数选项、光照、相机、像素数据 |
| `void setScene(RenderContext*, const ref<Scene>&) override` | 加载场景，创建计算通道程序，设置环境贴图和材质列表 |
| `bool onMouseEvent(const MouseEvent&) override` | 鼠标左键点击选择像素 |
| `bool onKeyEvent(const KeyboardEvent&) override` | 左右方向键切换材质 |
| `void readPixelData()` | 从暂存缓冲区回读选定像素的着色数据 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpScene` | `ref<Scene>` | 已加载的场景 |
| `mpEnvMap` | `ref<EnvMap>` | 环境贴图 |
| `mParams` | `BSDFViewerParams` | 与着色器共享的参数 |
| `mpSampleGenerator` | `ref<SampleGenerator>` | 采样生成器 |
| `mpFence` | `ref<Fence>` | GPU 围栏（同步数据回读） |
| `mpPixelDataBuffer` | `ref<Buffer>` | 选定像素数据的 GPU 缓冲区 |
| `mpPixelStagingBuffer` | `ref<Buffer>` | 数据回读暂存缓冲区 |
| `mPixelData` | `PixelData` | 回读的像素数据 |
| `mpViewerPass` | `ref<ComputePass>` | 计算着色器通道 |

## 依赖关系

### 本文件引用

- `Falcor.h` — 核心头文件
- `RenderGraph/RenderPass.h` — 渲染通道基类
- `Utils/Sampling/SampleGenerator.h` — 采样生成器
- `Utils/Debug/PixelDebug.h` — 像素调试
- `Scene/Lights/EnvMap.h` — 环境贴图
- `BSDFViewerParams.slang` — 共享参数结构体

### 被以下文件引用

- `BSDFViewer.cs.slang` — 对应的计算着色器

## 实现细节

- **Disney 漫反射切换**: 支持在 Disney 漫反射 BRDF 和 Falcor 默认（Frostbite 版本）之间切换，通过着色器宏定义 `DiffuseBrdf` 控制。
- **遮蔽-阴影函数**: 支持可分离和关联两种 Smith GGX 遮蔽-阴影函数变体。
- **反照率输出**: 材质模式下可选择输出总反照率而非反射率，支持四种反照率分量的独立控制。
- **异步像素回读**: 使用 GPU 围栏和暂存缓冲区实现异步像素数据回读，避免完整 GPU 刷新。
- **环境贴图加载**: 支持运行时通过文件对话框加载环境贴图。
