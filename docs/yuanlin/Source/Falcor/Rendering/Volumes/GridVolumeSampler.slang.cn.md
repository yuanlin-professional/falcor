# GridVolumeSampler.slang 源码文档

> 路径: `Source/Falcor/Rendering/Volumes/GridVolumeSampler.slang`
> 类型: Slang 着色器模块
> 模块: Rendering/Volumes

## 功能概述

本文件实现了网格体积采样器的 GPU 端核心算法，提供了透射率估计和散射距离采样的多种方法。支持的算法包括：

- **透射率估计**: Delta Tracking、Ratio Tracking、Ratio Tracking + 局部主值 DDA
- **距离采样**: Delta Tracking、Delta Tracking + 局部主值 DDA

所有算法通过编译时宏选择，由 C++ 端 `GridVolumeSampler::getDefines()` 控制。当前实现仅使用场景中的第一个网格体积。

## 结构体与接口

### `GridVolumeSampler`

| 成员/常量 | 类型 | 说明 |
|----------|------|------|
| `kTransmittanceEstimator` | `TransmittanceEstimator` | 编译时选择的透射率估计方法 |
| `kDistanceSampler` | `DistanceSampler` | 编译时选择的距离采样方法 |
| `kDDAMipStart` | `float` | 多级 DDA 起始 mip 层级（2.0） |
| `kDDAMipUpSpeed` | `float` | mip 层级上升速度（0.33） |
| `kDDAMipDownSpeed` | `float` | mip 层级下降速度（2.0） |

### `DistanceSample`

| 成员 | 类型 | 说明 |
|------|------|------|
| `t` | `float` | 采样的散射距离 |
| `thp` | `float3` | 吞吐量（反照率） |

### `DensityAccessor`

根据是否使用砖块化网格，类型不同：
- 砖块化网格: `float`（当前 mip 层级）
- NanoVDB: `Grid::Accessor`（NanoVDB 加速器）

## 函数

### 公共接口

| 函数 | 说明 |
|------|------|
| `intersectsVolumes(ray) -> bool` | 检查光线是否与体积相交 |
| `evalTransmittance<S>(ray, sg) -> float` | 沿光线评估透射率 |
| `sampleDistance<S>(ray, sg, out ds) -> bool` | 采样散射距离 |

### 坐标变换

| 函数 | 说明 |
|------|------|
| `transformRayToIndexSpace(gridVolume, grid, origin, dir, out ipos, out idir)` | 将光线从世界空间变换到索引空间 |

### DDA 步进

| 函数 | 说明 |
|------|------|
| `stepDDA(rayOrigin, invRayDir, pos, mip) -> float` | 在指定 mip 层级执行一次 DDA 步进 |
| `stepToNextCollision(...)` -> bool | 沿 DDA 步进直到发生碰撞或离开体积 |

### 密度查询

| 函数 | 说明 |
|------|------|
| `lookupDensityIndex(gridVolume, ipos, u, grid, accessor) -> float` | 在索引空间查询密度值（随机化采样） |

### 透射率估计算法

| 函数 | 说明 |
|------|------|
| `evalTransmittanceDeltaTracking<S>(...)` | Delta Tracking 透射率估计 |
| `evalTransmittanceRatioTracking<S>(...)` | Ratio Tracking 透射率估计（含俄罗斯轮盘赌） |
| `evalTransmittanceRatioTrackingLocalDDA<S>(...)` | 局部主值 DDA 加速的 Ratio Tracking |

### 距离采样算法

| 函数 | 说明 |
|------|------|
| `sampleDistanceDeltaTracking<S>(...)` | Delta Tracking 距离采样 |
| `sampleDistanceDeltaTrackingLocalDDA<S>(...)` | 局部主值 DDA 加速的 Delta Tracking |

## 依赖关系 / import

- `Utils/Math/MathConstants.slangh`
- `Scene.Scene`
- `Utils.Debug.PixelDebug`
- `Utils.Math.Ray`, `Utils.Math.MathHelpers`, `Utils.Math.HashUtils`
- `Utils.Geometry.IntersectionHelpers`
- `Utils.Sampling.TinyUniformSampleGenerator`
- `Rendering.Utils.PixelStats`（`logVolumeLookup()` 统计）
- `GridVolumeSamplerParams`

## 实现细节

- **DDA 多级遍历**: 使用砖块化网格时，通过自适应 mip 层级加速空白区域跳过。碰撞时降低 mip 层级以提高精度，无碰撞时升高以加速
- **Delta Tracking**: 使用全局主值（`densityScale * maxValue`）生成指数分布的步长，通过俄罗斯轮盘赌判断真/虚碰撞
- **Ratio Tracking**: 类似 Delta Tracking 但不终止采样，而是累乘透射率系数 `(1 - d/majorant)`，通过俄罗斯轮盘赌避免数值精度问题
- **局部主值**: 使用 `lookupIndexLocalMajorantTex()` 获取局部区域的主值上界，显著减少虚碰撞次数
- **砖块化网格坐标偏移**: NanoVDB 允许负索引但纹理不允许，因此对索引空间坐标减去 `minIndex`
- **密度查询统计**: 每次密度查询调用 `logVolumeLookup()` 记录体积查询次数
