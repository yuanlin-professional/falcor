# LightData.slang 源码文档

> 路径: `Source/Falcor/Scene/Lights/LightData.slang`
> 类型: Slang 着色器文件（主机/设备共享）
> 模块: Scene/Lights

## 功能概述

定义解析光源的类型枚举 `LightType` 和数据结构 `LightData`，在 CPU 和 GPU 之间共享。该结构体包含所有类型光源（点光、方向光、远距离光、面积光）所需的参数。

## 结构体与接口

### `LightType` (枚举)

| 值 | 说明 |
|----|------|
| `Point` | 点光源（可作为聚光灯使用） |
| `Directional` | 方向光源 |
| `Distant` | 远距离光源（占据非零立体角） |
| `Rect` | 矩形面积光源 |
| `Disc` | 圆盘面积光源 |
| `Sphere` | 球形面积光源 |

### `LightData`

| 成员 | 类型 | 说明 |
|------|------|------|
| `posW` | `float3` | 世界空间位置 |
| `type` | `uint32_t` | 光源类型 |
| `dirW` | `float3` | 世界空间方向（归一化） |
| `openingAngle` | `float` | 聚光灯锥形半角（弧度），默认 PI |
| `intensity` | `float3` | 发射辐照度（RGB） |
| `cosOpeningAngle` | `float` | cos(openingAngle) |
| `cosSubtendedAngle` | `float` | 远距离光的 cos(半角)，默认近似太阳 |
| `penumbraAngle` | `float` | 半影区域半角（弧度） |
| `tangent` | `float3` | 面积光形状的切线向量 |
| `surfaceArea` | `float` | 面积光表面积 |
| `bitangent` | `float3` | 面积光形状的副切线向量 |
| `transMat` | `float4x4` | 局部到世界空间的变换矩阵 |
| `transMatIT` | `float4x4` | 变换矩阵的逆转置 |

## 依赖关系 / import

- `Utils/HostDeviceShared.slangh` — 主机/设备共享宏定义
- `Utils/Math/MathConstants.slangh` — 数学常量（M_PI 等）
