# SceneDebugger 源码文档

> 路径: `Source/RenderPasses/SceneDebugger/SceneDebugger.h` + `SceneDebugger.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/SceneDebugger

## 功能概述

场景调试渲染通道，用于帮助识别资产问题（如不正确的法线、材质属性等）。该通道支持多种可视化模式，包括平面着色、三角形密度、命中类型、实例 ID、材质 ID、几何 ID、BLAS ID、法线方向、纹理坐标及 BSDF 属性等。同时支持可选的 VBuffer 输入，以及体积可视化和二次光线追踪性能分析。

## 类与接口

### `SceneDebugger`

- **继承**: `RenderPass`
- **职责**: 场景调试渲染通道，通过光线追踪或 VBuffer 获取命中信息，并以伪彩色或属性值的方式可视化场景中的几何体、材质和着色数据

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `SceneDebugger(ref<Device>, const Properties&)` | 构造函数，解析属性字典，要求 SM6.5 和 Raytracing Tier 1.1 |
| `Properties getProperties() const` | 序列化当前状态为属性字典 |
| `RenderPassReflection reflect(const CompileData&)` | 声明可选输入 `vbuffer`（RGBA32Uint）和输出 `output`（RGBA32Float） |
| `void compile(RenderContext*, const CompileData&)` | 记录帧尺寸和 VBuffer 可用性 |
| `void setScene(RenderContext*, const ref<Scene>&)` | 设置场景，创建计算着色器通道，初始化 mesh-to-BLAS 查找表和实例元数据 |
| `void execute(RenderContext*, const RenderData&)` | 执行调试渲染，调度计算着色器，回读选中像素数据 |
| `void renderUI(Gui::Widgets&)` | 渲染 UI 面板，包含模式选择、参数调整和像素数据显示 |
| `bool onMouseEvent(const MouseEvent&)` | 处理鼠标左键点击以选择像素 |
| `SceneDebuggerMode getMode() const` | 获取当前可视化模式（脚本接口） |
| `void setMode(SceneDebuggerMode)` | 设置可视化模式（脚本接口） |
| `void renderPixelDataUI(Gui::Widgets&)` | 在 UI 中显示选中像素的详细几何、网格实例和材质信息 |
| `void initInstanceInfo()` | 初始化实例元数据缓冲区，标记哪些几何体是被实例化的 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpPixelDebug` | `unique_ptr<PixelDebug>` | 像素调试工具（着色器中 print） |
| `mpSampleGenerator` | `ref<SampleGenerator>` | 采样器生成器 |
| `mpScene` | `ref<Scene>` | 当前场景 |
| `mParams` | `SceneDebuggerParams` | 调试参数（模式、帧尺寸、选中像素等） |
| `mpDebugPass` | `ref<ComputePass>` | 计算着色器通道 |
| `mpPixelData` | `ref<Buffer>` | 选中像素数据缓冲区（GPU 端） |
| `mpPixelDataStaging` | `ref<Buffer>` | 选中像素数据回读缓冲区 |
| `mpMeshToBlasID` | `ref<Buffer>` | Mesh 到 BLAS ID 的查找表 |
| `mpInstanceInfo` | `ref<Buffer>` | 实例信息缓冲区 |

## 依赖关系

### 本文件引用

- `Falcor.h` — Falcor 核心框架
- `RenderGraph/RenderPass.h` — 渲染通道基类
- `Utils/Debug/PixelDebug.h` — 像素调试工具
- `Scene/HitInfoType.slang` — 命中类型定义
- `SharedTypes.slang` — 共享枚举与结构体
- `RenderPasses/SceneDebugger/SceneDebugger.cs.slang` — 计算着色器

### 被以下文件引用

- 渲染图系统通过插件注册机制自动加载

## 实现细节

- 通过 `registerPlugin` 注册为 Falcor 插件，同时注册 Python 绑定以支持脚本控制 `mode` 属性
- 构造函数检查设备能力（SM6.5、Raytracing Tier 1.1），解析属性字典中的 `mode`、`showVolumes`、`useVBuffer`
- `setScene` 会创建计算通道、构建 mesh-to-BLAS 查找表（`getMeshBlasIDs`）和实例元数据（标记是否为实例化几何）
- `execute` 中如果 `useVBuffer` 为 true 则从 VBuffer 读取命中信息，否则使用内联光线追踪；执行后通过 `copyResource` 和 Fence 异步回读像素数据
- `renderPixelDataUI` 根据命中类型（Triangle/Curve/SDFGrid）显示不同的几何信息，包括网格拓扑、实例变换矩阵、场景图节点等
- 支持多种可视化模式，在着色器侧通过 `SceneDebuggerMode` 枚举选择
