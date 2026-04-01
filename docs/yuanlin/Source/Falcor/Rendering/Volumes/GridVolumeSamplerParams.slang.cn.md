# GridVolumeSamplerParams.slang 源码文档

> 路径: `Source/Falcor/Rendering/Volumes/GridVolumeSamplerParams.slang`
> 类型: Slang 着色器模块（主机/设备共享）
> 模块: Rendering/Volumes

## 功能概述

本文件定义了网格体积采样器的参数枚举类型，在 C++ 主机端和 Slang 着色器端共享使用。包含透射率估计方法和距离采样方法的枚举定义，以及判断是否需要砖块化网格的辅助函数。

## 结构体与接口

### 枚举 `TransmittanceEstimator`

| 值 | 说明 |
|----|------|
| `DeltaTracking` | Delta Tracking 算法 |
| `RatioTracking` | Ratio Tracking 算法 |
| `RatioTrackingLocalMajorant` | Ratio Tracking + 局部主值（仅砖块化网格） |

### 枚举 `DistanceSampler`

| 值 | 说明 |
|----|------|
| `DeltaTracking` | Delta Tracking 算法 |
| `DeltaTrackingLocalMajorant` | Delta Tracking + 局部主值（仅砖块化网格） |

## 函数

| 函数 | 说明 |
|------|------|
| `requiresBrickedGrid(DistanceSampler d) -> bool` | 判断距离采样器是否需要砖块化网格（仅主机端） |
| `requiresBrickedGrid(TransmittanceEstimator t) -> bool` | 判断透射率估计器是否需要砖块化网格（仅主机端） |

## 依赖关系 / import

- `Utils/HostDeviceShared.slangh`

## 实现细节

- 使用 `FALCOR_ENUM_INFO` 和 `FALCOR_ENUM_REGISTER` 宏注册枚举，支持 GUI 下拉菜单和序列化
- `requiresBrickedGrid()` 辅助函数通过 `#ifdef HOST_CODE` 仅在主机端编译
- 局部主值变体要求砖块化网格提供多级 mip 纹理，因此标记为 "BrickedGrid only"
