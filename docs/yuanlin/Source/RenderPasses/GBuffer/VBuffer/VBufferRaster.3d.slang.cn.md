# VBufferRaster.3d.slang 源码文档

> 路径: `Source/RenderPasses/GBuffer/VBuffer/VBufferRaster.3d.slang`
> 类型: Slang 3D 光栅化着色器
> 模块: RenderPasses/GBuffer/VBuffer

## 功能概述

V-buffer 光栅化渲染的顶点和像素着色器。顶点着色器计算世界空间位置和裁剪空间位置，并可选地计算前一帧位置用于运动矢量。像素着色器使用 SV_Barycentrics 打包三角形命中信息并输出到渲染目标。

## 结构体与接口

### `VBufferPSOut`

| 成员 | 语义 | 说明 |
|------|------|------|
| `packedHitInfo` | SV_TARGET0 | 打包的命中信息 |

### `VBufferVSOut`

| 成员 | 说明 |
|------|------|
| `texC` | 纹理坐标 |
| `instanceID` | 几何实例 ID |
| `materialID` | 材质 ID |
| `posH` | 裁剪空间位置 |
| `prevPosH` | 前一帧裁剪空间位置（运动矢量用） |

## 函数

| 函数 | 说明 |
|------|------|
| `vsMain(VSIn)` | 顶点着色器，执行世界变换和运动矢量准备 |
| `psMain(VBufferVSOut, uint, float3)` | 像素着色器，打包命中信息并计算运动矢量 |

## 依赖关系 / import

- `Scene.Raster` — 光栅化场景接口
- `Utils.Math.MathHelpers` — 数学工具
