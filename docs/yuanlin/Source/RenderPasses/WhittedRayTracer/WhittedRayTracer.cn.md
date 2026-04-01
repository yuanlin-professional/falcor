# WhittedRayTracer 源码文档

> 路径: `Source/RenderPasses/WhittedRayTracer/WhittedRayTracer.h` + `Source/RenderPasses/WhittedRayTracer/WhittedRayTracer.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/WhittedRayTracer

## 功能概述

WhittedRayTracer 是一个经典 Whitted 风格的光线追踪渲染通道。该通道主要用作纹理细节层次（LOD）方法的演示和测试平台。它覆盖场景材质，添加理想镜面反射和折射组件，不期望实现无偏渲染。支持三种纹理 LOD 模式：Mip0（固定 LOD）、RayCones（光线锥）和 RayDiffs（光线微分）。

## 类与接口

### `WhittedRayTracer`

- **继承**: `RenderPass`
- **职责**: 实现 Whitted 光线追踪，演示多种纹理 LOD 过滤方法

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `WhittedRayTracer(ref<Device>, const Properties&)` | 构造函数，解析属性并创建均匀采样生成器 |
| `Properties getProperties() const override` | 序列化所有配置参数 |
| `RenderPassReflection reflect(const CompileData&) override` | 声明输入通道（posW, normalW, tangentW 等 G-buffer 数据）和输出通道（color） |
| `void execute(RenderContext*, const RenderData&) override` | 设置静态参数、绑定资源、派发光线追踪 |
| `void renderUI(Gui::Widgets&) override` | 渲染 UI 控件：最大反弹数、纹理 LOD 模式、光线锥模式等 |
| `void setScene(RenderContext*, const ref<Scene>&) override` | 加载场景，创建光线追踪程序（仅支持三角形网格） |
| `void setStaticParams(Program*) const` | 设置编译时常量（宏定义）到着色器程序 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpScene` | `ref<Scene>` | 当前场景 |
| `mpSampleGenerator` | `ref<SampleGenerator>` | GPU 采样生成器 |
| `mMaxBounces` | `uint` | 最大间接反弹次数，默认 3 |
| `mTexLODMode` | `TexLODMode` | 纹理 LOD 模式，默认 Mip0 |
| `mRayConeMode` | `RayConeMode` | 光线锥变体，默认 Combo |
| `mRayConeFilterMode` | `RayFootprintFilterMode` | 光线锥过滤模式，默认各向同性 |
| `mRayDiffFilterMode` | `RayFootprintFilterMode` | 光线微分过滤模式，默认各向同性 |
| `mVisualizeSurfaceSpread` | `bool` | 是否可视化表面散射角 |
| `mUseRoughnessToVariance` | `bool` | 是否基于双向散射分布函数粗糙度扩展光线锥 |
| `mUseFresnelAsBRDF` | `bool` | 是否使用菲涅尔项作为 BRDF |

## 依赖关系

### 本文件引用

- `Falcor.h` — Falcor 核心头文件
- `RenderGraph/RenderPass.h` — 渲染通道基类
- `RenderGraph/RenderPassHelpers.h` — 渲染通道辅助工具
- `Utils/Sampling/SampleGenerator.h` — 采样生成器
- `Rendering/Materials/TexLODTypes.slang` — 纹理 LOD 枚举类型
- `WhittedRayTracerTypes.slang` — 光线足迹过滤模式枚举

### 被以下文件引用

- `WhittedRayTracer.rt.slang` — 对应的光线追踪着色器

## 实现细节

- **G-buffer 输入**: 需要完整的 G-buffer 数据作为输入，包括世界空间位置、法线、切线、面法线、纹理坐标、纹理梯度、材质数据和 V-buffer。
- **仅三角形支持**: 不支持程序化几何（曲线、SDF 等），遇到时输出警告。
- **静态参数**: 通过 `setStaticParams` 将 12 个编译时常量传递给着色器，包括 LOD 模式、过滤模式、光源启用状态等。
- **Python 绑定**: 通过 `registerBindings` 注册为 Python 类，支持脚本化控制。
- **屏幕空间像素角**: 计算相机像素在屏幕空间的展开角度，用于光线锥纹理 LOD 计算。
