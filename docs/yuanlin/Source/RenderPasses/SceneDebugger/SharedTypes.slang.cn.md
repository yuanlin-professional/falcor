# SharedTypes.slang 源码文档

> 路径: `Source/RenderPasses/SceneDebugger/SharedTypes.slang`
> 类型: Slang 着色器共享类型定义
> 模块: RenderPasses/SceneDebugger

## 功能概述

定义 SceneDebugger 渲染通道在 CPU 和 GPU 之间共享的枚举、参数结构体和数据类型。包括可视化模式枚举、BSDF 属性枚举、调试参数结构体、像素数据结构体和实例信息结构体。

## 结构体与接口

### `SceneDebuggerMode` (枚举)

场景调试可视化模式。

| 值 | 说明 |
|----|------|
| `FlatShaded` | 平面着色 |
| `TriangleDensity` | 三角形密度热力图 |
| `HitType` | 命中类型伪彩色 |
| `InstanceID` | 实例 ID 伪彩色 |
| `MaterialID` | 材质 ID 伪彩色 |
| `GeometryID` | 几何 ID 伪彩色 |
| `BlasID` | BLAS ID 伪彩色 |
| `PrimitiveID` | 图元 ID 伪彩色 |
| `InstancedGeometry` | 实例化几何标记（绿色=实例化，红色=非实例化） |
| `MaterialType` | 材质类型伪彩色 |
| `FaceNormal` | 面法线 RGB |
| `ShadingNormal` | 着色法线 RGB |
| `ShadingTangent` | 着色切线 RGB |
| `ShadingBitangent` | 着色副切线 RGB |
| `FrontFacingFlag` | 正面/背面标记 |
| `BackfacingShadingNormal` | 背面着色法线高亮 |
| `TexCoords` | 纹理坐标 RG |
| `BSDFProperties` | BSDF 属性 |

### `SceneDebuggerBSDFProperty` (枚举)

BSDF 属性可视化选项：`Emission`、`Roughness`、`GuideNormal`、漫反射/高光反射/透射反照率、`SpecularReflectance`、`IsTransmissive`。

### `SceneDebuggerParams`

| 成员 | 类型 | 说明 |
|------|------|------|
| `mode` | `uint` | 当前可视化模式 |
| `frameDim` | `uint2` | 帧尺寸 |
| `frameCount` | `uint` | 帧计数器 |
| `bsdfProperty` | `uint` | 当前 BSDF 属性 |
| `bsdfIndex` | `uint` | BSDF 层索引 |
| `selectedPixel` | `uint2` | 选中的像素坐标 |
| `flipSign` | `int` | 可视化前是否翻转符号 |
| `remapRange` | `int` | 是否将范围重映射到 [0,1] |
| `clamp` | `int` | 是否钳位到 [0,1] |
| `showVolumes` | `int` | 是否显示体积 |
| `volumeDensityScale` | `float` | 体积密度缩放 |
| `useVBuffer` | `int` | 是否使用 VBuffer |
| `profileSecondaryRays` | `int` | 是否追踪二次光线 |
| `profileSecondaryLoadHit` | `int` | 是否加载二次光线命中信息 |
| `profileSecondaryConeAngle` | `float` | 二次光线锥角（度） |
| `triangleDensityLogRange` | `float2` | 三角形密度 log2 范围 |

### `PixelData`

存储选中像素的详细信息，包括命中类型、实例 ID、材质 ID、几何 ID、BLAS ID 以及着色数据（世界位置、法线、切线、纹理坐标等）。

### `InstanceInfo`

| 成员 | 类型 | 说明 |
|------|------|------|
| `flags` | `uint` | 实例标记（如 `IsInstanced`） |

### `InstanceInfoFlags` (枚举)

| 值 | 说明 |
|----|------|
| `IsInstanced` | 该几何体有多个实例（0x1） |

## 依赖关系 / import

- `Utils/HostDeviceShared.slangh` — 主机/设备共享宏（`BEGIN_NAMESPACE_FALCOR` 等）
