# GBufferBase 源码文档

> 路径: `Source/RenderPasses/GBuffer/GBufferBase.h` + `Source/RenderPasses/GBuffer/GBufferBase.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/GBuffer

## 功能概述

G-buffer 渲染通道的基类，为所有不同类型的 G-buffer 通道（包括 V-buffer）提供通用基础设施。管理输出尺寸、采样模式（抗锯齿相机抖动）、Alpha 测试、着色法线调整和剔除模式等通用选项。同时负责插件注册，将 GBufferRaster、GBufferRT、VBufferRaster、VBufferRT 四种渲染通道注册到框架中。

## 类与接口

### `GBufferBase`

- **继承**: `RenderPass`
- **职责**: 为 G-buffer/V-buffer 渲染通道提供公共配置、UI 和执行逻辑。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `renderUI(Gui::Widgets&)` | 渲染通用 UI 控件（输出尺寸、采样模式、Alpha 测试、法线调整、剔除模式） |
| `execute(RenderContext*, const RenderData&)` | 通用执行逻辑，更新刷新标志和着色法线调整标志 |
| `getProperties()` | 获取通用属性配置 |
| `setScene(RenderContext*, const ref<Scene>&)` | 设置场景并更新采样模式 |
| `parseProperties(const Properties&)` | 解析通用属性 |
| `updateFrameDim(uint2)` | 更新帧尺寸并设置相机抖动 |
| `updateSamplePattern()` | 更新采样模式生成器 |
| `getOutput(const RenderData&, const string&)` | 获取并验证输出纹理 |

### `SamplePattern`（枚举）

| 值 | 说明 |
|----|------|
| `Center` | 中心采样（禁用抗锯齿） |
| `DirectX` | DirectX 采样模式 |
| `Halton` | Halton 低差异序列 |
| `Stratified` | 分层采样 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpScene` | `ref<Scene>` | 当前场景 |
| `mpSampleGenerator` | `ref<CPUSampleGenerator>` | 相机抖动采样生成器 |
| `mFrameCount` | `uint32_t` | 场景变更后的已渲染帧数 |
| `mFrameDim` | `uint2` | 当前帧尺寸 |
| `mVBufferFormat` | `ResourceFormat` | V-buffer 格式 |
| `mSamplePattern` | `SamplePattern` | 当前采样模式 |
| `mUseAlphaTest` | `bool` | 是否启用 Alpha 测试 |
| `mAdjustShadingNormals` | `bool` | 是否调整着色法线 |
| `mForceCullMode` | `bool` | 是否强制剔除模式 |
| `mCullMode` | `RasterizerState::CullMode` | 剔除模式 |

## 依赖关系

### 本文件引用

- `Falcor.h`, `RenderGraph/RenderPass.h`, `RenderGraph/RenderPassHelpers.h`
- `Utils/SampleGenerators/DxSamplePattern.h`, `HaltonSamplePattern.h`, `StratifiedSamplePattern.h`

### 被以下文件引用

- `GBuffer/GBuffer.h` — G-buffer 中间基类
- `VBuffer/VBufferRT.h`, `VBuffer/VBufferRaster.h` — V-buffer 实现

## 实现细节

- `execute()` 方法设置渲染字典中的刷新标志和着色法线调整标志，供后续渲染通道使用
- 采样模式通过 `CPUSampleGenerator` 实现，在每帧开始时为相机设置抖动偏移
- 插件注册在 `.cpp` 文件中通过 `registerPlugin` 函数完成
