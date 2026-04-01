# PixelInspectorData.slang 源码文档

> 路径: `Source/RenderPasses/PixelInspectorPass/PixelInspectorData.slang`
> 类型: Slang 共享类型定义
> 模块: RenderPasses/PixelInspectorPass

## 功能概述

定义像素检查器渲染通道在 CPU 和 GPU 之间共享的 `PixelData` 结构体，存储选中像素的完整几何、材质和输出数据。

## 结构体与接口

### `PixelData`

| 成员 | 类型 | 说明 |
|------|------|------|
| `kInvalidIndex` | `static const uint` | 无效索引常量 (0xFFFFFFFF) |
| `posW` | `float3` | 世界空间位置 |
| `normal` | `float3` | 着色法线 |
| `tangent` | `float3` | 着色切线 |
| `bitangent` | `float3` | 着色副切线 |
| `faceNormal` | `float3` | 面法线 |
| `view` | `float3` | 视线方向 |
| `texCoord` | `float2` | 纹理坐标 |
| `frontFacing` | `int` | 是否正面 |
| `materialID` | `uint` | 材质 ID |
| `doubleSided` | `int` | 是否双面 |
| `opacity` | `float` | 不透明度 |
| `IoR` | `float` | 折射率 |
| `emission` | `float3` | 自发光 |
| `roughness` | `float` | 粗糙度 |
| `guideNormal` | `float3` | 引导法线 |
| `diffuseReflectionAlbedo` | `float3` | 漫反射反照率 |
| `diffuseTransmissionAlbedo` | `float3` | 漫透射反照率 |
| `specularReflectionAlbedo` | `float3` | 高光反射反照率 |
| `specularTransmissionAlbedo` | `float3` | 高光透射反照率 |
| `specularReflectance` | `float3` | 高光反射率 |
| `isTransmissive` | `int` | 是否具有透射性 |
| `linearColor` | `float4` | 线性颜色 |
| `outputColor` | `float4` | 输出颜色 |
| `luminance` | `float` | 亮度 |
| `hitType` | `uint` | 命中类型 |
| `instanceID` | `uint` | 实例 ID |
| `primitiveIndex` | `uint` | 图元索引 |
| `barycentrics` | `float2` | 重心坐标 |

## 依赖关系 / import

- `Utils/HostDeviceShared.slangh`
- `Scene/HitInfoType.slang` / `Scene.HitInfoType`
