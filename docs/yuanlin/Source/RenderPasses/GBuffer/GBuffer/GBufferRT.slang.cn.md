# GBufferRT.slang 源码文档

> 路径: `Source/RenderPasses/GBuffer/GBuffer/GBufferRT.slang`
> 类型: Slang 着色器
> 模块: RenderPasses/GBuffer

## 功能概述

光线追踪 G-buffer 的核心着色器逻辑。定义了 `GBufferRT` 结构体，包含所有 G-buffer 输出通道的 UAV 纹理声明，以及光线生成、命中数据写入、未命中数据写入等完整逻辑。支持光线微分和光线锥两种纹理 LOD 计算方法，以及景深（DOF）光线生成。

## 结构体与接口

### `GBufferRT`

| 成员 | 类型 | 说明 |
|------|------|------|
| `frameDim` | `uint2` | 帧尺寸 |
| `invFrameDim` | `float2` | 帧尺寸倒数 |
| `frameCount` | `uint` | 帧计数 |
| `screenSpacePixelSpreadAngle` | `float` | 屏幕空间像素扩展角 |

## 函数

| 函数 | 说明 |
|------|------|
| `generateRay(uint2 pixel)` | 生成相机光线（支持针孔和薄透镜模型） |
| `computeRayDifferentials(...)` | 基于光线微分计算纹理坐标梯度 |
| `computeAnisotropicAxesRayCones(...)` | 基于光线锥计算各向异性纹理坐标梯度 |
| `writeHit(pixel, rayOrigin, rayDir, hit, hitT)` | 处理命中，加载顶点数据、准备着色数据、创建材质实例并写入所有 G-buffer 通道 |
| `writeMiss(pixel, rayOrigin, rayDir)` | 写入未命中（环境贴图）的默认值 |
| `writeAux(pixel, ray)` | 写入辅助数据（视线方向） |
| `computeMotionVectorBackground(...)` | 计算背景像素的运动矢量 |
| `beginTime/endTime(...)` | GPU 计时器控制 |

## 依赖关系 / import

- `Scene.Shading` — 着色数据
- `Utils.Timing.GpuTimer` — GPU 计时器
- `Utils.Sampling.SampleGenerator` — 采样生成器
- `Rendering.Materials.TexLODTypes`, `TexLODHelpers` — 纹理 LOD
- `GBufferHelpers` — G-buffer 辅助函数
