# ModulateIllumination 源码文档

> 路径: `Source/RenderPasses/ModulateIllumination/ModulateIllumination.h` + `ModulateIllumination.cpp`
> 类型: C++ 头文件 + 实现
> 模块: RenderPasses/ModulateIllumination

## 功能概述

光照调制渲染通道，将分离的光照分量（自发光、漫反射、高光、Delta 反射/透射、残余辐射）重新合成为最终图像。每个分量可独立启用/禁用，反射率通道与辐照度通道相乘后累加。常用于降噪后重新合成最终颜色。

## 类与接口

### `ModulateIllumination`

- **继承**: `RenderPass`
- **职责**: 将多个光照分量合成为最终输出颜色

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `ModulateIllumination(ref<Device>, const Properties&)` | 构造函数，创建计算着色器并解析属性 |
| `Properties getProperties() const` | 序列化所有启用/禁用标记 |
| `RenderPassReflection reflect(const CompileData&)` | 声明 12 个可选输入和 1 个输出（RGBA32Float） |
| `void execute(RenderContext*, const RenderData&)` | 设置定义列表、绑定纹理并执行计算着色器 |
| `void renderUI(Gui::Widgets&)` | 渲染 12 个复选框控制各分量 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpModulateIlluminationPass` | `ref<ComputePass>` | 计算着色器通道 |
| `mFrameDim` | `uint2` | 帧尺寸 |
| `mUseEmission` | `bool` | 启用自发光 |
| `mUseDiffuseReflectance` | `bool` | 启用漫反射反照率 |
| `mUseDiffuseRadiance` | `bool` | 启用漫反射辐照度 |
| `mUseSpecularReflectance` | `bool` | 启用高光反照率 |
| `mUseSpecularRadiance` | `bool` | 启用高光辐照度 |
| `mUseDeltaReflection*` | `bool` | 启用 Delta 反射分量 |
| `mUseDeltaTransmission*` | `bool` | 启用 Delta 透射分量 |
| `mUseResidualRadiance` | `bool` | 启用残余辐照度 |

### 输入通道

| 通道名 | 着色器变量 | 说明 |
|--------|-----------|------|
| `emission` | `gEmission` | 自发光 |
| `diffuseReflectance` | `gDiffuseReflectance` | 漫反射反照率 |
| `diffuseRadiance` | `gDiffuseRadiance` | 漫反射辐照度 |
| `specularReflectance` | `gSpecularReflectance` | 高光反照率 |
| `specularRadiance` | `gSpecularRadiance` | 高光辐照度 |
| `deltaReflectionEmission` | `gDeltaReflectionEmission` | Delta 反射自发光 |
| `deltaReflectionReflectance` | `gDeltaReflectionReflectance` | Delta 反射反照率 |
| `deltaReflectionRadiance` | `gDeltaReflectionRadiance` | Delta 反射辐照度 |
| `deltaTransmissionEmission` | `gDeltaTransmissionEmission` | Delta 透射自发光 |
| `deltaTransmissionReflectance` | `gDeltaTransmissionReflectance` | Delta 透射反照率 |
| `deltaTransmissionRadiance` | `gDeltaTransmissionRadiance` | Delta 透射辐照度 |
| `residualRadiance` | `gResidualRadiance` | 残余辐照度 |

## 依赖关系

### 本文件引用

- `Falcor.h`、`RenderGraph/RenderPass.h`、`RenderGraph/RenderPassHelpers.h`
- `ModulateIllumination.cs.slang` — 合成计算着色器

### 被以下文件引用

- 渲染图系统通过插件注册机制加载

## 实现细节

- 通过 `getValidResourceDefines` 生成 `is_valid_*` 宏，通知着色器哪些输入可用
- CPU 端根据 `mUse*` 标记覆盖定义列表，实现运行时启用/禁用
- 定义变更时重新编译着色器（`addDefines` 返回 true 时清除变量）
- 输入纹理尺寸不匹配帧尺寸时记录错误日志
