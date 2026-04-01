# NRDPass 源码文档

> 路径: `Source/RenderPasses/NRDPass/NRDPass.h` + `Source/RenderPasses/NRDPass/NRDPass.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/NRDPass

## 功能概述

实现 NVIDIA Real-time Denoisers (NRD) 降噪渲染通道的 Falcor 集成。NRD 是一组基于时空滤波的高质量实时降噪算法，支持 ReLAX（漫反射+镜面）、ReLAX（仅漫反射）和 ReBLUR（漫反射+镜面）等多种降噪方法。该通道管理 NRD 库的初始化、资源创建、参数设置和 GPU 调度，并提供额外的辐射度打包计算通道。

## 类与接口

### `NRDPass`

- **继承**: `RenderPass`
- **职责**: 封装 NRD 库的完整生命周期，提供降噪管线的配置和执行。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `reflect(const CompileData&)` | 声明输入输出端口（运动矢量、法线粗糙度、viewZ、漫反射/镜面辐射度+命中距离等） |
| `compile(...)` | 根据输出尺寸重新初始化 NRD |
| `execute(...)` | 执行降噪管线（辐射度打包 + NRD 调度） |
| `setScene(...)` | 设置场景 |
| `renderUI(...)` | 渲染降噪方法选择和参数调整 UI |
| `reinit()` | 重新创建 NRD 降噪器实例 |
| `createPipelines()` | 从 NRD 库获取管线描述并创建计算通道 |
| `createResources()` | 创建永久和临时纹理资源 |
| `dispatch(...)` | 执行单个 NRD 调度（绑定资源、设置描述符、执行计算通道） |

### `DenoisingMethod`（枚举）

| 值 | 说明 |
|----|------|
| `RelaxDiffuseSpecular` | ReLAX 漫反射 + 镜面降噪 |
| `RelaxDiffuse` | ReLAX 仅漫反射降噪 |
| `ReblurDiffuseSpecular` | ReBLUR 漫反射 + 镜面降噪 |
| `SpecularReflectionMv` | 镜面反射运动矢量 |
| `SpecularDeltaMv` | 镜面 Delta 运动矢量 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpDenoiser` | `nrd::Denoiser*` | NRD 降噪器实例 |
| `mEnabled` | `bool` | 是否启用降噪 |
| `mDenoisingMethod` | `DenoisingMethod` | 当前降噪方法 |
| `mWorldSpaceMotion` | `bool` | 是否使用世界空间运动矢量 |
| `mMaxIntensity` | `float` | 最大光照强度钳制值 |
| `mDisocclusionThreshold` | `float` | 去遮挡阈值 |
| `mCommonSettings` | `nrd::CommonSettings` | NRD 通用设置 |
| `mRelaxDiffuseSpecularSettings` | `nrd::RelaxDiffuseSpecularSettings` | ReLAX 参数 |
| `mReblurSettings` | `nrd::ReblurSettings` | ReBLUR 参数 |
| `mpPackRadiancePassRelax/Reblur` | `ref<ComputePass>` | 辐射度打包计算通道 |
| `mpPermanentTextures` | `vector<ref<Texture>>` | 永久纹理（跨帧保持） |
| `mpTransientTextures` | `vector<ref<Texture>>` | 临时纹理（帧内使用） |

## 依赖关系

### 本文件引用

- `Falcor.h`, `RenderGraph/RenderPassHelpers.h`
- `Core/API/Shared/D3D12DescriptorSet.h`, `D3D12RootSignature.h`, `D3D12ConstantBufferView.h` — D3D12 底层 API
- `<NRD.h>` — NRD 库头文件
- `PackRadiance.cs.slang` — 辐射度打包着色器

## 实现细节

- 直接使用 D3D12 底层 API（描述符集、根签名、CBV）与 NRD 库交互
- NRD 管线的着色器从 NRD 库以字节码形式获取，不通过 Falcor 着色器编译系统
- 辐射度打包通道将辐射度和命中距离转换为 NRD 所需的打包格式
- 支持 ReLAX 和 ReBLUR 两种打包格式
- 通过 `nrd::SetDenoiserSettings` 传递降噪参数
- 视图矩阵和投影矩阵从 Falcor 相机获取并转换为行主序
