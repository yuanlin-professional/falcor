# GridVolume 源码文档

> 路径: `Source/Falcor/Scene/Volume/GridVolume.h`, `Source/Falcor/Scene/Volume/GridVolume.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Scene/Volume

## 功能概述

网格体积（Grid Volume）类，描述场景中的异构参与介质。通过密度体素网格定义吸收/散射介质，通过发射体素网格定义自发光。支持静态网格和动态网格序列（动画体积），以及各种物理参数设置（密度缩放、散射反照率、各向异性、发射模式和温度）。

## 类与接口

### `GridVolume`

- **继承**: `Animatable`
- **职责**: 管理体积的网格数据、物理参数和动画播放。

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `static ref<GridVolume> create(ref<Device>, const string& name)` | 创建体积 |
| `bool renderUI(Gui::Widgets& widget)` | 渲染 UI，返回是否有修改 |
| `bool loadGrid(GridSlot slot, const path&, const string& gridname)` | 加载单个网格到指定插槽 |
| `uint32_t loadGridSequence(GridSlot, const vector<path>&, const string&, bool keepEmpty)` | 加载网格序列 |
| `uint32_t loadGridSequence(GridSlot, const path& dir, const string&, bool keepEmpty)` | 从目录加载网格序列 |
| `void setGridSequence(GridSlot, const GridSequence&)` | 设置网格序列 |
| `void setGrid(GridSlot, const ref<Grid>&)` | 设置单个网格 |
| `const ref<Grid>& getGrid(GridSlot) const` | 获取当前帧的网格 |
| `vector<ref<Grid>> getAllGrids() const` | 获取所有唯一网格 |
| `void setGridFrame(uint32_t)` | 设置当前网格帧 |
| `void setFrameRate(double)` | 设置播放帧率 |
| `void setPlaybackEnabled(bool)` | 启用/禁用播放 |
| `void updatePlayback(double currentTime)` | 根据全局时间更新帧 |
| `void setDensityScale(float)` | 设置密度缩放因子 |
| `void setEmissionScale(float)` | 设置发射缩放因子 |
| `void setAlbedo(const float3&)` | 设置散射反照率 |
| `void setAnisotropy(float)` | 设置相位函数各向异性（-0.99 到 0.99） |
| `void setEmissionMode(EmissionMode)` | 设置发射模式（Direct/Blackbody） |
| `void setEmissionTemperature(float)` | 设置发射基础温度（K） |
| `const GridVolumeData& getData() const` | 获取体积数据结构 |
| `const AABB& getBounds() const` | 获取世界空间包围盒 |

#### 关键枚举

| 枚举 | 值 | 说明 |
|------|------|------|
| `UpdateFlags` | None, PropertiesChanged, GridsChanged, TransformChanged, BoundsChanged | 更新标志位 |
| `GridSlot` | Density, Emission | 网格插槽 |
| `EmissionMode` | Direct, Blackbody | 发射模式 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mGrids` | `array<GridSequence, Count>` | 每个插槽的网格序列 |
| `mGridFrame` | `uint32_t` | 当前帧索引 |
| `mGridFrameCount` | `uint32_t` | 序列帧总数 |
| `mFrameRate` | `double` | 播放帧率（1-1000） |
| `mBounds` | `AABB` | 世界空间包围盒 |
| `mData` | `GridVolumeData` | GPU 共享数据结构 |

## 依赖关系

### 本文件引用

- `Grid.h` — 体素网格
- `GridVolumeData.slang` — 体积数据结构
- `Scene/Animation/Animatable.h` — 动画支持
- `Utils/Math/AABB.h` — 包围盒
- `Utils/Scripting/ScriptBindings.h` — Python 绑定

### 被以下文件引用

- `Scene/Scene.h` — 场景管理
- 着色器端的 `GridVolume.slang`
- 体积渲染通道

## 实现细节

- 网格序列帧数取所有插槽序列的最大长度。
- 包围盒在网格或变换变化时重新计算，合并所有插槽的世界空间包围盒并应用变换。
- 目录加载时按文件名长度优先、字母顺序排序（确保数字序列正确排序）。
- 各向异性参数限制在 [-0.99, 0.99] 范围内（避免极端前向/后向散射退化）。
- 通过 `FALCOR_SCRIPT_BINDING` 导出完整的 Python 接口。
