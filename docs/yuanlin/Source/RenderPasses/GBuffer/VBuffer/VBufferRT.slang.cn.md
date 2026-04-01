# VBufferRT.slang 源码文档

> 路径: `Source/RenderPasses/GBuffer/VBuffer/VBufferRT.slang`
> 类型: Slang 着色器
> 模块: RenderPasses/GBuffer/VBuffer

## 功能概述

光线追踪 V-buffer 的核心着色器逻辑。定义 `VBufferRT` 结构体，包含光线生成、命中/未命中数据写入以及辅助数据输出。与 GBufferRT 不同，V-buffer 仅存储紧凑的命中信息（实例 ID + 图元索引 + 重心坐标），以及简化的深度和运动矢量。

## 结构体与接口

### `VBufferRT`

| 成员 | 类型 | 说明 |
|------|------|------|
| `frameDim` | `uint2` | 帧尺寸 |
| `frameCount` | `uint` | 帧计数 |

## 函数

| 函数 | 说明 |
|------|------|
| `generateRay(uint2)` | 生成相机光线（支持针孔和薄透镜） |
| `writeHit(pixel, rayOrigin, rayDir, hit)` | 写入命中数据：打包的 HitInfo、深度、运动矢量 |
| `writeMiss(pixel, rayOrigin, rayDir)` | 写入未命中默认值 |
| `writeAux(pixel, ray)` | 写入视线方向 |
| `beginTime/endTime(...)` | GPU 计时 |

## 依赖关系 / import

- `Utils.Timing.GpuTimer`, `Utils.Math.Ray` — 工具模块
- `Utils.Sampling.SampleGenerator` — 采样生成器
- `Scene.Shading` — 着色数据
