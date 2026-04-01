# RTXDIPass 源码文档

> 路径: `Source/RenderPasses/RTXDIPass/RTXDIPass.h` + `RTXDIPass.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/RTXDIPass

## 功能概述

本文件实现了基于 RTXDI（RTX Direct Illumination）的直接光照渲染通道。该通道利用 ReSTIR（Reservoir-based Spatiotemporal Importance Resampling）算法高效采样直接光照，是 Falcor 中 RTXDI 模块的使用示例。

渲染流程由两个计算通道组成：
1. **PrepareSurfaceData**: 从 VBuffer 提取表面数据供 RTXDI 使用
2. **FinalShading**: 使用 RTXDI 最终采样进行可见性检查和材质 BSDF 着色

## 类与接口

### `RTXDIPass`

- **继承**: `RenderPass`
- **职责**: 作为独立的 RTXDI 直接光照渲染通道

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `RTXDIPass(ref<Device>, const Properties&)` | 构造函数，解析属性 |
| `Properties getProperties() const` | 序列化 RTXDI 选项 |
| `RenderPassReflection reflect(const CompileData&)` | 声明输入（vbuffer、texGrads、mvec）和输出通道 |
| `void compile(RenderContext*, const CompileData&)` | 记录帧尺寸 |
| `void execute(RenderContext*, const RenderData&)` | 执行渲染：检查场景更新 -> 准备表面数据 -> RTXDI 更新 -> 最终着色 |
| `void renderUI(Gui::Widgets&)` | 显示 RTXDI 模块 GUI |
| `void setScene(RenderContext*, const ref<Scene>&)` | 设置场景并创建 RTXDI 实例 |
| `bool onMouseEvent(const MouseEvent&)` | 转发鼠标事件到 RTXDI 像素调试 |
| `void prepareSurfaceData(RenderContext*, const ref<Texture>&)` | 执行表面数据准备计算通道 |
| `void finalShading(RenderContext*, const ref<Texture>&, const RenderData&)` | 执行最终着色计算通道 |
| `void recreatePrograms()` | 重建着色器程序（场景变化时） |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpScene` | `ref<Scene>` | 当前场景 |
| `mpRTXDI` | `unique_ptr<RTXDI>` | RTXDI 模块实例 |
| `mOptions` | `RTXDI::Options` | RTXDI 配置选项 |
| `mpPrepareSurfaceDataPass` | `ref<ComputePass>` | 表面数据准备计算通道 |
| `mpFinalShadingPass` | `ref<ComputePass>` | 最终着色计算通道 |
| `mFrameDim` | `uint2` | 帧尺寸 |
| `mGBufferAdjustShadingNormals` | `bool` | 是否调整着色法线 |

#### 输入通道

| 名称 | 着色器变量 | 说明 | 可选 |
|------|-----------|------|------|
| `vbuffer` | `gVBuffer` | 可见性缓冲区（packed 格式） | 否 |
| `texGrads` | `gTextureGrads` | 纹理梯度 | 是 |
| `mvec` | `gMotionVector` | 运动向量缓冲区 | 是 |

#### 输出通道

| 名称 | 着色器变量 | 说明 |
|------|-----------|------|
| `color` | `gColor` | 最终颜色 |
| `emission` | `gEmission` | 自发光颜色 |
| `diffuseIllumination` | `gDiffuseIllumination` | 漫反射光照 |
| `diffuseReflectance` | `gDiffuseReflectance` | 漫反射反射率 |
| `specularIllumination` | `gSpecularIllumination` | 镜面反射光照 |
| `specularReflectance` | `gSpecularReflectance` | 镜面反射反射率 |

## 依赖关系

### 本文件引用

- `Falcor.h` — 核心框架
- `RenderGraph/RenderPass.h` — 渲染通道基类
- `RenderGraph/RenderPassHelpers.h` — 渲染通道辅助工具
- `RenderGraph/RenderPassStandardFlags.h` — 标准刷新标志
- `Rendering/RTXDI/RTXDI.h` — RTXDI 模块
- 着色器: `PrepareSurfaceData.cs.slang`、`FinalShading.cs.slang`

### 被以下文件引用

- 渲染图配置脚本通过 `RTXDIPass` 名称引用

## 实现细节

1. **场景限制**: 仅支持三角形几何体，过程化几何体会被忽略并产生警告。

2. **着色器重建**: 当场景标记 `RecompileNeeded` 或 `GeometryChanged` 时，重新创建着色器程序以匹配新的场景定义。

3. **选项同步**: UI 操作更改的选项通过 `RenderPassRefreshFlags::RenderOptionsChanged` 标志通知其他通道。

4. **GBuffer 法线调整**: 通过渲染数据字典读取 `kRenderPassGBufferAdjustShadingNormals` 标志，并传递给着色器定义。
