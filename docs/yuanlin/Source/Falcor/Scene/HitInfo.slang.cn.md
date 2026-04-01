# HitInfo.slang 源码文档

> 路径: `Source/Falcor/Scene/HitInfo.slang`
> 类型: Slang 着色器文件
> 模块: Scene

## 功能概述

HitInfo.slang 定义了 GPU 端光线命中信息的多态容器。它是光线追踪管线中最核心的数据结构之一，用于在着色器中紧凑地编码和解码各种几何类型的命中信息，包括三角形、位移三角形、曲线、SDF 网格和体积。

根据是否启用压缩，`PackedHitInfo` 可以是 `uint2`（64 位）或 `uint4`（128 位）。

## 结构体与接口

### `GeometryHit`

几何命中信息的基类，包含实例 ID、图元索引和重心坐标。

| 成员 | 类型 | 说明 |
|------|------|------|
| `instanceID` | `GeometryInstanceID` | 几何实例标识 |
| `primitiveIndex` | `uint` | 图元索引 |
| `barycentrics` | `float2` | 重心坐标 |

### `TriangleHit : GeometryHit`

三角形命中信息。未压缩时重心坐标为 32 位浮点，压缩时为 16 位 unorm。

### `DisplacedTriangleHit : GeometryHit`

位移三角形命中信息。额外存储位移偏移量，重心坐标以 24 位 unorm 编码。

### `CurveHit : GeometryHit`

曲线命中信息。编码格式与 TriangleHit（未压缩）相同。

### `SDFGridHit`

SDF 网格命中信息。包含实例 ID 和 `SDFGridHitData`，根据不同的 SDF 实现存储不同的数据。

### `VolumeHit`

体积命中信息。存储命中距离 `t` 和各向异性参数 `g`。

### `HitInfo`

多态命中信息容器，核心结构体。管理所有命中类型的打包与解包。

| 成员/常量 | 说明 |
|-----------|------|
| `kTypeBits` | 编码命中类型所需位数 |
| `kInstanceIDBits` | 编码实例 ID 所需位数 |
| `kPrimitiveIndexBits` | 编码图元索引所需位数 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `HitInfo::isValid()` | 判断是否为有效命中 |
| `HitInfo::getType()` | 获取命中类型枚举 |
| `HitInfo::getTriangleHit()` | 获取三角形命中数据 |
| `HitInfo::getDisplacedTriangleHit()` | 获取位移三角形命中数据 |
| `HitInfo::getCurveHit()` | 获取曲线命中数据 |
| `HitInfo::getSDFGridHit()` | 获取 SDF 网格命中数据 |
| `HitInfo::getVolumeHit()` | 获取体积命中数据 |
| `HitInfo::packHeader()` | 静态方法，将类型和实例信息打包到头部 |
| `HitInfo::unpackHeader()` | 静态方法，从头部解包实例信息 |
| `makeInvalidHit()` | 创建无效命中信息 |
| `unpackHitInfo(PackedHitInfo)` | 从打包数据还原 HitInfo |

## 依赖关系 / import

- `Scene/SceneDefines.slangh` - 场景几何类型宏定义
- `Utils.Math.FormatConversion` - 格式转换工具
- `Scene.HitInfoType` - 命中类型枚举（导出）
- `Scene.SceneTypes` - 场景类型定义（导出）
- `Scene.SDFs.SDFGridHitData` - SDF 网格命中数据（导出）
