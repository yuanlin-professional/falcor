# GridVolumeSampler 源码文档

> 路径: `Source/Falcor/Rendering/Volumes/GridVolumeSampler.h` + `Source/Falcor/Rendering/Volumes/GridVolumeSampler.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Rendering/Volumes

## 功能概述

`GridVolumeSampler` 是网格体积采样器的主机端管理类，用于配置和控制网格体积（如 VDB 体积）中的透射率估计和散射距离采样。它管理采样算法的选择（Delta Tracking、Ratio Tracking 及其局部主值变体），提供着色器宏定义和 GUI 控件。

## 类与接口

### `GridVolumeSampler`

- **继承**: 无
- **职责**: 管理网格体积采样器的配置选项，提供着色器定义和数据绑定

#### 内部结构体 `Options`

| 成员 | 类型 | 说明 |
|------|------|------|
| `transmittanceEstimator` | `TransmittanceEstimator` | 透射率估计方法（默认 `RatioTrackingLocalMajorant`） |
| `distanceSampler` | `DistanceSampler` | 距离采样方法（默认 `DeltaTrackingLocalMajorant`） |
| `useBrickedGrid` | `bool` | 是否使用砖块化网格（默认 true） |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `GridVolumeSampler(RenderContext*, ref<IScene>, const Options&)` | 构造函数 |
| `DefineList getDefines() const` | 获取着色器宏定义（算法选择） |
| `void bindShaderData(const ShaderVar&) const` | 绑定着色器数据（当前为空操作） |
| `bool renderUI(Gui::Widgets&)` | 渲染 GUI 控件 |
| `const Options& getOptions() const` | 获取当前选项 |
| `void setOptions(const Options&)` | 设置选项 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpScene` | `ref<IScene>` | 场景引用 |
| `mOptions` | `Options` | 当前配置选项 |

## 依赖关系

### 本文件引用

- `GridVolumeSamplerParams.slang`（枚举定义）
- `Core/Macros.h`, `Core/Error.h`
- `Core/Program/DefineList.h`
- `Utils/Properties.h`, `Utils/UI/Gui.h`
- `Scene/Scene.h`

### 被以下文件引用

- 路径追踪器及其他需要体积渲染的渲染通道

## 实现细节

- 输出三个着色器宏定义：
  - `GRID_VOLUME_SAMPLER_USE_BRICKEDGRID`: 是否使用砖块化网格
  - `GRID_VOLUME_SAMPLER_TRANSMITTANCE_ESTIMATOR`: 透射率估计算法编号
  - `GRID_VOLUME_SAMPLER_DISTANCE_SAMPLER`: 距离采样算法编号
- 在 GUI 中切换算法时，自动检查是否需要砖块化网格支持：
  - 选择局部主值变体时自动启用砖块化网格
  - 禁用砖块化网格时自动回退到基础算法
