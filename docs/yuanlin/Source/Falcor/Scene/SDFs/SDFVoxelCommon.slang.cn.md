# SDFVoxelCommon.slang 源码文档

> 路径: `Source/Falcor/Scene/SDFs/SDFVoxelCommon.slang`
> 类型: Slang 着色器文件
> 模块: Scene/SDFs

## 功能概述

提供 SDF 体素操作的公共工具函数,包括位置编码/解码(Morton 码)、体素值的打包/解包、表面包含检测以及三线性插值。这些函数被所有 SDF 网格实现共享使用。

## 结构体与接口

### `SDFVoxelCommon` (struct)

#### 常量

| 常量 | 值 | 说明 |
|------|-----|------|
| `kMaxLevel` | 19 | SDF 网格的最大层级,网格宽度不超过 `2^19` |
| `kLocationCodeVoxelCoordMask` | `(1 << 19) - 1` | 位置码中体素坐标的掩码 |
| `kLocationCodeLevelOffset` | `3 * 19 = 57` | 位置码中层级字段的偏移量 |

#### 函数

| 方法签名 | 说明 |
|----------|------|
| `static uint2 encodeLocation(uint3 coords, uint level)` | 将局部体素坐标和层级编码为 64 位位置码(Morton 码) |
| `static void decodeLocation(uint2 code, out uint3 coords, out uint level)` | 从 64 位位置码解码出局部坐标和层级 |
| `static bool createChildLocationCode(uint2 code, uint childID, out uint2 childCode)` | 创建指定子节点的位置码 |
| `static uint64_t shiftCoord(uint x)` | 将坐标位扩展为交错格式(用于 Morton 码编码) |
| `static uint unshiftCoord(uint64_t x)` | 从交错格式恢复坐标值 |
| `static uint3 levelLocalToGlobalCoords(uint3 coords, uint level)` | 层级局部坐标转全局坐标 |
| `static uint3 globalToLevelLocalCoords(uint3 coords, uint level)` | 全局坐标转层级局部坐标 |
| `static uint2 packValues(float4 values0xx, float4 values1xx)` | 将 8 个距离值打包为 8 字节 snorm 格式 |
| `static void unpackValues(uint2 packed, out float4 v0, out float4 v1)` | 从 8 字节 snorm 格式解包 8 个距离值 |
| `static bool containsSurface(float4 v0, float4 v1)` | 保守检测体素是否包含隐式表面(正负值同时存在) |
| `static float sdfVoxelTrilin(float4 v0, float4 v1, float3 uv)` | 对 8 个角点值进行三线性插值 |

## 依赖关系

### import
- `Utils.Math.FormatConversion`
- `Utils.Math.AABB`

### include
- `Utils/Math/MathConstants.slangh`

### 被以下文件引用
- 几乎所有 SDF 相关着色器文件
