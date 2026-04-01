# LightCollectionShared.slang 源码文档

> 路径: `Source/Falcor/Scene/Lights/LightCollectionShared.slang`
> 类型: Slang 着色器文件（主机/设备共享）
> 模块: Scene/Lights

## 功能概述

定义自发光三角形的数据结构，包括未打包的 `EmissiveTriangle`、打包的 `PackedEmissiveTriangle`（64 字节）和通量数据 `EmissiveFlux`。这些结构在 CPU 和 GPU 之间共享。

## 结构体与接口

### `EmissiveTriangle`

| 成员 | 类型 | 说明 |
|------|------|------|
| `posW[3]` | `float3` | 三个顶点的世界空间位置 |
| `texCoords[3]` | `float2` | 三个顶点的纹理坐标 |
| `normal` | `float3` | 世界空间面法线 |
| `area` | `float` | 世界空间三角形面积 |
| `materialID` | `uint` | 材质 ID |
| `lightIdx` | `uint` | 全局网格光源数组索引 |

| 方法 | 说明 |
|------|------|
| `float3 getPosition(float3 barycentrics)` | 重心坐标插值位置 |
| `float2 getTexCoord(float3 barycentrics)` | 重心坐标插值纹理坐标 |

### `PackedEmissiveTriangle`（64 字节）

| 成员 | 类型 | 说明 |
|------|------|------|
| `posAndTexCoords[3]` | `float4` | 位置（xyz）+ 纹理坐标（fp16 编码在 w 中） |
| `normal` | `uint` | 法线（16 位 snorm 八面体编码） |
| `area` | `uint` | 面积（fp32 编码） |
| `materialID` | `uint` | 材质 ID |
| `lightIdx` | `uint` | 光源索引 |

| 方法 | 说明 |
|------|------|
| `void pack(const EmissiveTriangle)` | 打包三角形数据（仅 GPU） |
| `EmissiveTriangle unpack()` | 解包三角形数据 |

### `EmissiveFlux`

| 成员 | 类型 | 说明 |
|------|------|------|
| `flux` | `float` | 预积分辐射通量 |
| `averageRadiance` | `float3` | 平均发射辐照度 |

## 依赖关系 / import

- `Utils/HostDeviceShared.slangh` — 主机/设备共享宏
- `Utils/Math/PackedFormats.h` / `Utils.Math.PackedFormats` — 编码/解码工具
