# HelloDXR 源码文档

> 路径: `Source/Samples/HelloDXR/HelloDXR.h` + `Source/Samples/HelloDXR/HelloDXR.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Samples/HelloDXR

## 功能概述

HelloDXR 是 Falcor 框架中的光线追踪入门示例。该程序展示了如何同时使用光栅化和光线追踪两种渲染模式渲染同一场景,用户可以通过空格键或 GUI 在两种模式之间切换。光线追踪模式支持反射和阴影,并可选启用景深(Depth of Field)效果。

## 类与接口

### `HelloDXR`

- **继承**: `SampleApp`
- **职责**: 加载场景,创建光栅化和光线追踪渲染管线,处理用户交互,并在每帧选择对应模式进行渲染

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `HelloDXR(const SampleAppConfig& config)` | 构造函数 |
| `~HelloDXR()` | 析构函数 |
| `void onLoad(RenderContext* pRenderContext) override` | 检查光线追踪支持并加载默认场景(`Arcade/Arcade.pyscene`) |
| `void onResize(uint32_t width, uint32_t height) override` | 窗口大小改变时更新相机参数和光线追踪输出纹理 |
| `void onFrameRender(RenderContext* pRenderContext, const ref<Fbo>& pTargetFbo) override` | 每帧根据模式选择光栅化或光线追踪渲染 |
| `void onGuiRender(Gui* pGui) override` | 渲染 GUI 控件(光线追踪开关、景深开关、场景加载按钮) |
| `bool onKeyEvent(const KeyboardEvent& keyEvent) override` | 空格键切换光栅化/光线追踪模式 |
| `bool onMouseEvent(const MouseEvent& mouseEvent) override` | 转发鼠标事件至场景 |
| `void loadScene(const std::filesystem::path& path, const Fbo* pTargetFbo)` | 加载场景并创建光栅化通道和光线追踪程序/绑定表 |
| `void setPerFrameVars(const Fbo* pTargetFbo)` | 设置光线追踪每帧常量缓冲区变量(逆视图矩阵、视口尺寸、半FOV正切值等) |
| `void renderRaster(RenderContext* pRenderContext, const ref<Fbo>& pTargetFbo)` | 执行光栅化渲染 |
| `void renderRT(RenderContext* pRenderContext, const ref<Fbo>& pTargetFbo)` | 执行光线追踪渲染 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpScene` | `ref<Scene>` | 当前加载的场景 |
| `mpCamera` | `ref<Camera>` | 场景相机 |
| `mpRasterPass` | `ref<RasterPass>` | 光栅化渲染通道 |
| `mpRaytraceProgram` | `ref<Program>` | 光线追踪着色器程序 |
| `mpRtVars` | `ref<RtProgramVars>` | 光线追踪程序变量绑定 |
| `mpRtOut` | `ref<Texture>` | 光线追踪输出纹理(RGBA16Float, UAV+SRV) |
| `mRayTrace` | `bool` | 是否使用光线追踪模式(默认 `true`) |
| `mUseDOF` | `bool` | 是否启用景深效果(默认 `false`) |
| `mSampleIndex` | `uint32_t` | 采样索引,用于随机采样生成器 |

## 依赖关系

### 本文件引用

- `Falcor.h` — Falcor 核心框架
- `Core/SampleApp.h` — 示例应用基类
- `Core/Pass/RasterPass.h` — 光栅化渲染通道
- `Utils/Math/FalcorMath.h` — 数学工具(焦距到 FOV 转换)
- `Utils/UI/TextRenderer.h` — 文本渲染(帧率显示)
- `HelloDXR.3d.slang` — 光栅化着色器
- `HelloDXR.rt.slang` — 光线追踪着色器

### 被以下文件引用

- 无(顶层示例入口)

## 实现细节

- 在 `loadScene` 中同时创建光栅化和光线追踪管线:
  - 光栅化使用 `RasterPass`,着色器入口为 `vsMain`/`psMain`。
  - 光线追踪使用 `Program` + `RtBindingTable`,配置了 2 种光线类型(主光线、阴影光线)和 2 种 miss 着色器。
  - 最大追踪递归深度为 3(RayGen -> 主光线 ClosestHit -> 反射光线追踪阴影光线)。
  - 最大负载大小为 24 字节(对应 `PrimaryRayData` 结构体)。
- 光线追踪 Shader Binding Table (SBT) 配置:
  - 光线索引 0: 主光线(primaryClosestHit + primaryAnyHit / primaryMiss)
  - 光线索引 1: 阴影光线(shadowAnyHit / shadowMiss)
- 场景使用 Falcor 材质系统,需要将 shader modules 和 type conformances 传递给程序。
