# CameraData.slang 源码文档

> 路径: `Source/Falcor/Scene/Camera/CameraData.slang`
> 类型: Slang 着色器文件（主机/设备共享结构）
> 模块: Scene/Camera

## 功能概述

定义 CPU 和 GPU 共享的相机数据结构 `CameraData`，包含相机的所有参数：变换矩阵、位置、投影属性、光线追踪基向量、抖动偏移和曝光参数。

## 结构体与接口

### `CameraData`

| 成员 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `viewMat` | `float4x4` | — | 视图矩阵 |
| `prevViewMat` | `float4x4` | — | 前帧视图矩阵 |
| `projMat` | `float4x4` | — | 投影矩阵 |
| `viewProjMat` | `float4x4` | — | 视图投影矩阵 |
| `invViewProj` | `float4x4` | — | 逆视图投影矩阵 |
| `viewProjMatNoJitter` | `float4x4` | — | 无抖动视图投影矩阵 |
| `prevViewProjMatNoJitter` | `float4x4` | — | 前帧无抖动视图投影矩阵 |
| `projMatNoJitter` | `float4x4` | — | 无抖动投影矩阵 |
| `posW` | `float3` | `(0,0,0)` | 世界空间位置 |
| `focalLength` | `float` | `21.0` | 焦距（毫米），默认约 59度垂直/90度水平 FOV（16:9） |
| `prevPosW` | `float3` | `(0,0,0)` | 前帧世界空间位置 |
| `up` | `float3` | `(0,1,0)` | 世界空间上方向 |
| `aspectRatio` | `float` | `1.7778` | 宽高比（frameWidth / frameHeight） |
| `target` | `float3` | `(0,0,-1)` | 目标点 |
| `nearZ` | `float` | `0.1` | 近裁剪面 |
| `cameraU` | `float3` | `(0,0,1)` | 光线追踪基向量 U（右方向，长度取决于 FOV） |
| `farZ` | `float` | `1000.0` | 远裁剪面 |
| `cameraV` | `float3` | `(0,1,0)` | 光线追踪基向量 V（上方向） |
| `jitterX` | `float` | `0.0` | X 轴亚像素抖动（除以屏幕宽度） |
| `cameraW` | `float3` | `(1,0,0)` | 光线追踪基向量 W（前方向，长度为焦距） |
| `jitterY` | `float` | `0.0` | Y 轴亚像素抖动（除以屏幕高度） |
| `frameHeight` | `float` | `24.0` | 胶片高度（毫米，35mm 标准） |
| `frameWidth` | `float` | `42.667` | 胶片宽度（毫米） |
| `focalDistance` | `float` | `10000.0` | 焦点距离（场景单位） |
| `apertureRadius` | `float` | `0.0` | 光圈半径（场景单位，0 为针孔） |
| `shutterSpeed` | `float` | `0.004` | 快门速度（秒） |
| `ISOSpeed` | `float` | `100.0` | ISO 感光度 |

## 依赖关系

### import

- `Utils/HostDeviceShared.slangh` — 主机/设备共享宏定义

## 实现细节

- 结构体大小要求为 `sizeof(float4)` 的整数倍，确保 GPU 内存对齐
- 包含 2 个填充字段（`_padding0`、`_padding1`、`_padding2`）
- 矩阵存储包含当前帧和前帧版本，用于运动向量和时间抗锯齿
- `cameraU/V/W` 向量经过 FOV 缩放，直接用于光线方向计算
