# LightBVHSampler 源码文档

> 路径: `Source/Falcor/Rendering/Lights/LightBVHSampler.h` + `LightBVHSampler.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Rendering/Lights

## 功能概述

本文件实现了基于光源 BVH 的自发光光源采样器。该采样器在内部构建和维护一棵光源 BVH 树，通过随机遍历 BVH 来高效采样自发光三角形。支持构建选项和遍历选项的独立配置，包括包围锥剔除、光照锥剔除、节点通量权重、均匀/重要性三角形选择等策略。当光源几何变化时支持增量 refit 而非全量重建。

## 类与接口

### `LightBVHSampler`

- **继承**: `EmissiveLightSampler`
- **职责**: 使用 BVH 加速结构进行自发光光源采样

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `LightBVHSampler(RenderContext*, ref<ILightCollection>, const Options&)` | 构造函数，创建 BVH 和构建器 |
| `virtual bool update(RenderContext*, ref<ILightCollection>) override` | 根据光源变化重建或 refit BVH |
| `virtual DefineList getDefines() const override` | 返回基类定义加上遍历选项相关的着色器宏 |
| `virtual void bindShaderData(const ShaderVar& var) const override` | 绑定 BVH 数据到着色器变量 `_lightBVH` |
| `virtual bool renderUI(Gui::Widgets& widget) override` | 渲染构建选项、遍历选项和统计信息的 GUI |
| `void setOptions(const Options& options)` | 设置选项，标记需要重建 |

### `LightBVHSampler::Options`

- **职责**: 存储 BVH 采样器配置

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `buildOptions` | `LightBVHBuilder::Options` | BVH 构建选项 |
| `useBoundingCone` | `bool` | 使用包围锥约束 NdotL 计算概率，默认 true |
| `useLightingCone` | `bool` | 使用光照锥剔除背面光源，默认 true |
| `disableNodeFlux` | `bool` | 是否禁用节点通量权重，默认 false |
| `useUniformTriangleSampling` | `bool` | 叶节点内均匀选择三角形，默认 true |
| `solidAngleBoundMethod` | `SolidAngleBoundMethod` | 立体角约束方法，默认 Sphere |

#### 其他关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpBVHBuilder` | `unique_ptr<LightBVHBuilder>` | BVH 构建器实例 |
| `mpBVH` | `unique_ptr<LightBVH>` | BVH 数据结构实例 |
| `mNeedsRebuild` | `bool` | 重建标志 |

## 实现细节

- **update()**:
  - `LayoutChanged` -> 完全重建
  - `MatrixChanged` -> 如果允许 refit 则 refit，否则重建
- **getDefines()**: 在基类定义基础上追加 `_USE_BOUNDING_CONE`、`_USE_LIGHTING_CONE`、`_DISABLE_NODE_FLUX`、`_USE_UNIFORM_TRIANGLE_SAMPLING`、`_ACTUAL_MAX_TRIANGLES_PER_NODE`、`_SOLID_ANGLE_BOUND_METHOD`
- 全局常量 `kLightingAccelerationStructure` 用于通过渲染通道字典传递加速结构指针

## 依赖关系

### 本文件引用

- `EmissiveLightSampler.h` — 基类
- `LightBVH.h` — BVH 数据结构
- `LightBVHBuilder.h` — BVH 构建器
- `LightBVHSamplerSharedDefinitions.slang` — 共享枚举定义
- `Core/Macros.h`、`Core/Error.h`
- `Utils/Properties.h`、`Utils/Math/AABB.h`、`Utils/Timing/Profiler.h`
- `Scene/Lights/LightCollection.h`

### 被以下文件引用

- `EmissiveLightSampler.slang` — 着色器端通过条件编译引用对应的 GPU 采样器
