# LightBVHBuilder 源码文档

> 路径: `Source/Falcor/Rendering/Lights/LightBVHBuilder.h` + `LightBVHBuilder.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Rendering/Lights

## 功能概述

本文件实现了光源 BVH 的 CPU 端构建器。支持三种分割启发式方法：等分（Equal）、基于表面积启发式的分箱法（Binned SAH）、以及基于表面积-方向启发式的分箱法（Binned SAOH）。SAOH 方法参考了 Conty Estevez 和 Kulla 2018 年的自适应树分割论文以及 Moreau 和 Clarberg 在 Ray Tracing Gems 第 18 章中的工作。构建器还负责计算每个节点的光照法线包围锥（bounding cone）。

## 类与接口

### `LightBVHBuilder`

- **继承**: 无
- **职责**: 使用可配置的分割策略在 CPU 端构建光源 BVH

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `LightBVHBuilder(const Options& options)` | 构造函数 |
| `void build(RenderContext* pRenderContext, LightBVH& bvh)` | 构建 BVH，包含分割、节点创建和光照锥计算 |
| `bool renderUI(Gui::Widgets& widget)` | 渲染构建选项 GUI |
| `const Options& getOptions() const` | 获取当前配置 |

### `LightBVHBuilder::SplitHeuristic` (枚举)

| 值 | 说明 |
|----|------|
| `Equal` | 将输入等分为两组 |
| `BinnedSAH` | 使用分箱的表面积启发式（SAH）分割 |
| `BinnedSAOH` | 使用分箱的表面积-方向启发式（SAOH）分割，考虑光照锥和辐射通量 |

### `LightBVHBuilder::Options`

- **职责**: 存储 BVH 构建配置

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `splitHeuristicSelection` | `SplitHeuristic` | 分割启发式选择，默认 BinnedSAOH |
| `maxTriangleCountPerLeaf` | `uint32_t` | 每个叶节点最大三角形数，默认 10 |
| `binCount` | `uint32_t` | 分箱数量，默认 16 |
| `volumeEpsilon` | `float` | 零维度 AABB 的替代值，默认 1e-3 |
| `splitAlongLargest` | `bool` | 是否仅沿最大维度分割 |
| `useVolumeOverSA` | `bool` | 是否使用体积代替表面积 |
| `useLeafCreationCost` | `bool` | 当分割代价高于叶创建代价时是否停止分割 |
| `createLeavesASAP` | `bool` | 尽可能早地创建叶节点 |
| `allowRefitting` | `bool` | 允许 refit 而非总是重建 |
| `usePreintegration` | `bool` | 使用预积分辐射通量进行剔除和分割计算 |
| `useLightingCones` | `bool` | 在分割计算中使用光照锥 |

## 实现细节

- **build()**: 收集发光三角形数据 -> 选择分割函数 -> 递归构建 -> 计算光照锥 -> 上传 GPU
- **buildInternal()**: 递归构建过程，每次计算节点 AABB 和通量，调用分割函数决定是否分割，分割时使用 `nth_element` 进行中位数划分
- **computeSplitWithEqual()**: 沿最大维度等分
- **computeSplitWithBinnedSAH()**: 沿各维度分箱，左右扫描计算 SAH 代价 `A(L)*N(L) + A(R)*N(R)`
- **computeSplitWithBinnedSAOH()**: 在 SAH 基础上加入方向代价和辐射通量权重，代价为 `flux * area * orientationCost`
- **computeLightingConesInternal()**: 递归计算内部节点的光照包围锥，使用 `coneUnionOld` 合并子节点锥
- **coneUnion()**: 实现 Sony EGSR 2018 论文中的锥合并算法（Algorithm 1），但当前使用的是较旧的 `coneUnionOld` 版本
- **最大树深度**: 限制为 64 层（受位掩码表示限制）

## 依赖关系

### 本文件引用

- `LightBVH.h` — BVH 数据结构
- `Core/Macros.h`、`Core/Enum.h`、`Core/Error.h`
- `Utils/Math/AABB.h`、`Utils/Math/Vector.h`、`Utils/Math/MathConstants.slangh`
- `Utils/UI/Gui.h`、`Utils/Logger.h`、`Utils/Timing/Profiler.h`

### 被以下文件引用

- `LightBVHSampler.h` — BVH 采样器包含构建器实例
