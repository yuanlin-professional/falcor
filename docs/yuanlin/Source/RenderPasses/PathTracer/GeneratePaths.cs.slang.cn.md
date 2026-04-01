# GeneratePaths.cs.slang 源码文档

> 路径: `Source/RenderPasses/PathTracer/GeneratePaths.cs.slang`
> 类型: Slang 计算着色器
> 模块: RenderPasses/PathTracer

## 功能概述

实现路径追踪器的路径生成阶段。该计算着色器以屏幕 Tile（16x16 像素）为单位调度，根据 V-buffer 中的主命中信息初始化路径。对于未命中表面的背景像素，直接评估环境贴图颜色并写入输出缓冲区。对于有效命中，准备 RTXDI 所需的表面数据。同时通过 Wave 操作和共享内存计算可变样本数下的前缀和偏移。

## 结构体与接口

### `PathGenerator`

| 成员 | 类型 | 说明 |
|------|------|------|
| `params` | `PathTracerParams` | 运行时参数 |
| `vbuffer` | `Texture2D<PackedHitInfo>` | 主命中点的 V-buffer |
| `viewDir` | `Texture2D<float3>` | 可选视线方向 |
| `sampleCount` | `Texture2D<uint>` | 可选输入每像素采样数 |
| `sampleOffset` | `RWTexture2D<uint>` | 输出样本偏移表 |
| `sampleColor` | `RWStructuredBuffer<ColorType>` | 输出每样本颜色 |
| `sampleGuideData` | `RWStructuredBuffer<GuideData>` | 输出降噪引导数据 |
| `outputNRD` | `NRDBuffers` | NRD 降噪输出缓冲区 |
| `outputColor` | `RWTexture2D<float4>` | 单样本输出颜色 |

## 函数

| 函数 | 说明 |
|------|------|
| `execute(uint2 tileID, uint threadIdx)` | 主入口，处理 Tile 内所有像素的路径初始化和样本偏移计算 |
| `writeBackground(uint2 pixel, uint spp, uint outIdx, float3 dir)` | 写入背景像素的颜色和降噪数据 |

## 依赖关系 / import

- `Utils.Attributes`, `Utils.Color.ColorHelpers` — 工具函数
- `Rendering.RTXDI.RTXDI` — RTXDI 采样
- `RenderPasses.Shared.Denoising.NRDBuffers`, `NRDConstants` — NRD 缓冲区定义
- `LoadShadingData` — 加载着色数据
- `NRDHelpers` — NRD 辅助函数
- `PathTracer`, `PathState`, `ColorType`, `Params` — 本模块核心类型
