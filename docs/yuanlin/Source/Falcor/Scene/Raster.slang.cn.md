# Raster.slang 源码文档

> 路径: `Source/Falcor/Scene/Raster.slang`
> 类型: Slang 着色器文件
> 模块: Scene

## 功能概述

Raster.slang 定义了 Falcor 光栅化管线的标准顶点着色器和相关辅助函数。它提供了默认的顶点输入/输出结构体、标准顶点变换逻辑、以及从插值后的顶点属性准备着色数据（ShadingData）和执行 Alpha 测试的辅助函数。

## 结构体与接口

### `VSIn`

顶点着色器输入结构体，对应打包的顶点属性。

| 成员 | 语义 | 说明 |
|------|------|------|
| `pos` | `POSITION` | 顶点位置 |
| `packedNormalTangentCurveRadius` | `PACKED_NORMAL_TANGENT_CURVE_RADIUS` | 打包的法线/切线/曲线半径 |
| `texC` | `TEXCOORD` | 纹理坐标 |
| `instanceID` | `DRAW_ID` | 实例 ID |
| `vertexID` | `SV_VertexID` | 系统顶点索引 |

### `VSOut`

顶点着色器输出结构体，包含世界空间的插值属性。

| 成员 | 语义 | 说明 |
|------|------|------|
| `normalW` | `NORMAL` | 世界空间着色法线 |
| `tangentW` | `TANGENT` | 世界空间着色切线 |
| `texC` | `TEXCRD` | 纹理坐标 |
| `posW` | `POSW` | 世界空间位置 |
| `prevPosH` | `PREVPOSH` | 上一帧裁剪空间位置 |
| `instanceID` | `INSTANCE_ID` | 几何实例 ID（无插值） |
| `materialID` | `MATERIAL_ID` | 材质 ID（无插值） |
| `posH` | `SV_POSITION` | 裁剪空间位置 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `VSOut defaultVS(VSIn vIn)` | 默认顶点着色器：执行世界/裁剪空间变换，计算上一帧位置（用于运动模糊） |
| `VertexData prepareVertexData(VSOut, float3 faceNormalW)` | 从插值属性准备 VertexData 结构体 |
| `ShadingData prepareShadingData(VSOut, uint triangleIndex, float3 viewDir)` | 准备完整的着色数据（含材质信息） |
| `bool alphaTest<L:ITextureSampler>(VSOut, uint triangleIndex, L lod)` | 执行 Alpha 测试，返回是否丢弃该命中 |

## 依赖关系 / import

- `VertexAttrib.slangh` - 顶点属性语义定义
- `Scene.Shading`（导出） - 着色相关模块（含 Scene、ShadingData、MaterialSystem 等）
