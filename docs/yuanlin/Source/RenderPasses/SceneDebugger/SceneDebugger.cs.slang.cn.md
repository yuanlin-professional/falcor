# SceneDebugger.cs.slang 源码文档

> 路径: `Source/RenderPasses/SceneDebugger/SceneDebugger.cs.slang`
> 类型: 计算着色器 (Compute Shader)
> 模块: RenderPasses/SceneDebugger

## 功能概述

场景调试渲染通道的计算着色器实现。每个像素执行一次，通过光线追踪或 VBuffer 获取命中信息，根据当前可视化模式输出对应的调试颜色。支持平面着色、几何属性伪彩色、着色法线/切线可视化、BSDF 属性查询、三角形密度热力图、体积透射率渲染以及二次光线性能分析。

## 结构体与接口

### `SceneDebugger`

主结构体，封装所有调试逻辑。

| 成员 | 类型 | 说明 |
|------|------|------|
| `params` | `SceneDebuggerParams` | 调试参数 |
| `meshToBlasID` | `StructuredBuffer<uint>` | Mesh 到 BLAS ID 查找缓冲区 |
| `instanceInfo` | `StructuredBuffer<InstanceInfo>` | 实例元数据缓冲区 |
| `vbuffer` | `Texture2D<PackedHitInfo>` | 可选的可见性缓冲区 |
| `output` | `RWTexture2D<float4>` | 输出纹理 |
| `pixelData` | `RWStructuredBuffer<PixelData>` | 选中像素数据（单元素） |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void execute(uint2 pixel)` | 主执行函数，追踪主光线并根据模式输出颜色 |
| `float3 remapVector(float3 v)` | 向量重映射（翻转符号 / 映射到 [0,1]） |
| `float3 pseudocolor(uint value)` | 通过 Jenkins 哈希生成伪彩色 |
| `float3 handleHit(uint2, float3, float3, HitInfo)` | 处理命中，加载顶点/着色数据，写入像素数据，返回可视化颜色 |
| `float3 handleMiss(uint2, float3)` | 未命中时绘制棋盘格背景 |
| `float3 handleVolumes(float3, float3, float3, float)` | 处理体积渲染，计算网格体积透射率 |
| `float evalGridVolumeTransmittance(...)` | 光线步进计算单个网格体积的透射率 |
| `static float3 getPosW(float3, float3, HitInfo)` | 从命中信息获取世界空间位置 |
| `ShadingData loadHit(Ray, HitInfo)` | 加载命中的着色数据 |
| `float3 traceSecondary(ShadingData, uint2)` | 追踪二次光线用于性能分析 |
| `float computeTriangleDensity(HitInfo)` | 计算三角形密度（面积倒数的 log2 归一化） |

### 入口点

| 入口点 | 说明 |
|--------|------|
| `[numthreads(16, 16, 1)] void main(uint3)` | 计算着色器主入口，调用 `gSceneDebugger.execute()` |

## 依赖关系 / import

- `Utils/HostDeviceShared.slangh` — 主机/设备共享宏
- `Utils/Math/MathConstants.slangh` — 数学常量
- `Utils.Math.HashUtils` — Jenkins 哈希函数
- `Utils.Math.ShadingFrame` — 着色坐标系
- `Utils.Geometry.IntersectionHelpers` — 射线-AABB 相交
- `Utils.Debug.PixelDebug` — 着色器内 print
- `Utils.Color.ColorMap` — 颜色映射（Viridis）
- `Utils.Color.ColorHelpers` — 颜色辅助函数
- `Utils.Sampling.SampleGenerator` — 采样生成器
- `SharedTypes` — 共享枚举/结构体
- `Scene.RaytracingInline` — 内联光线追踪
- `Scene.Scene` — 场景数据
- `Utils.Math.Ray` — 光线结构体
