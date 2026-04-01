# GBufferRaster.3d.slang 源码文档

> 路径: `Source/RenderPasses/GBuffer/GBuffer/GBufferRaster.3d.slang`
> 类型: Slang 3D 光栅化着色器
> 模块: RenderPasses/GBuffer

## 功能概述

G-buffer 光栅化渲染通道的像素着色器。使用 `[earlydepthstencil]` 属性进行提前深度测试，利用 SV_Barycentrics 语义获取重心坐标。将标准 G-buffer 数据写入 8 个渲染目标（SV_TARGET0-7），额外数据通过 ROV（RasterizerOrderedTexture2D）输出为 UAV。

## 结构体与接口

### `GBufferPSOut`

| 成员 | 语义 | 说明 |
|------|------|------|
| `posW` | SV_TARGET0 | 世界空间位置 |
| `normW` | SV_TARGET1 | 着色法线 |
| `tangentW` | SV_TARGET2 | 切线 |
| `faceNormalW` | SV_TARGET3 | 面法线 |
| `texC` | SV_TARGET4 | 纹理坐标 |
| `texGrads` | SV_TARGET5 | 纹理梯度（通过 ddx/ddy 计算） |
| `mvec` | SV_TARGET6 | 运动矢量 |
| `mtlData` | SV_TARGET7 | 材质数据 |

## 函数

| 函数 | 说明 |
|------|------|
| `vsMain(VSIn)` | 顶点着色器，调用默认 VS |
| `psMain(VSOut, uint, float3)` | 像素着色器，准备着色数据、创建材质实例、写入所有 G-buffer 通道 |
| `computeMotionVector(VSOut, int2)` | 计算运动矢量 |

## 依赖关系 / import

- `Scene.Raster` — 光栅化场景接口
- `Utils.Math.MathHelpers` — 数学工具
- `GBufferHelpers` — G-buffer 辅助函数
- `Rendering.Materials.TexLODHelpers` — 纹理 LOD 辅助
