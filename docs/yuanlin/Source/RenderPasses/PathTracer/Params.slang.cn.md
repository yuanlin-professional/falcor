# Params.slang 源码文档

> 路径: `Source/RenderPasses/PathTracer/Params.slang`
> 类型: Slang 着色器（主机/设备共享）
> 模块: RenderPasses/PathTracer

## 功能概述

定义路径追踪器在主机端和设备端共享的参数、常量和枚举类型。包括颜色格式枚举、MIS 启发式枚举、屏幕 Tile 尺寸、路径配置限制以及核心运行时参数结构体 `PathTracerParams`。

## 结构体与接口

### `ColorFormat`（枚举）

| 值 | 说明 |
|----|------|
| `RGBA32F` | 32 位浮点 RGBA 格式 |
| `LogLuvHDR` | LogLuv HDR 压缩格式 |

### `MISHeuristic`（枚举）

| 值 | 说明 |
|----|------|
| `Balance` | 平衡启发式 |
| `PowerTwo` | 功率启发式（指数 = 2） |
| `PowerExp` | 功率启发式（可变指数） |

### `PathTracerParams`

| 成员 | 类型 | 说明 |
|------|------|------|
| `useFixedSeed` | `int` | 是否使用固定随机种子 |
| `fixedSeed` | `uint` | 固定种子值 |
| `lodBias` | `float` | 二次命中的 LOD 偏移 |
| `specularRoughnessThreshold` | `float` | 镜面粗糙度阈值 |
| `frameDim` | `uint2` | 帧尺寸（像素） |
| `screenTiles` | `uint2` | 屏幕 Tile 数量 |
| `frameCount` | `uint` | 已渲染帧数 |
| `seed` | `uint` | 随机种子 |

### 常量

| 常量 | 值 | 说明 |
|------|----|------|
| `kScreenTileDim` | `{16, 16}` | 屏幕 Tile 尺寸 |
| `kMaxSamplesPerPixel` | `16` | 最大每像素采样数 |
| `kMaxFrameDimension` | `4096` | 最大帧尺寸 |
| `kMaxBounces` | `254` | 每类反弹的最大次数 |
| `kMaxLightSamplesPerVertex` | `8` | 每顶点最大光源采样数 |

## 依赖关系 / import

- `Utils/HostDeviceShared.slangh` — 主机/设备共享宏
- `StaticParams` — 静态编译时参数（GPU 端）
- `Utils.Math.BitTricks` — 位操作工具（GPU 端）
