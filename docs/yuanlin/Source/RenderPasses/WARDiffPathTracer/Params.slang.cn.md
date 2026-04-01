# Params.slang 源码文档

> 路径: `Source/RenderPasses/WARDiffPathTracer/Params.slang`
> 类型: Slang 着色器头文件
> 模块: RenderPasses/WARDiffPathTracer

## 功能概述

定义 WARDiffPathTracer 在主机和设备之间共享的运行时参数结构体和路径配置限制常量。该文件同时导出静态参数和位操作工具模块。

## 结构体与接口

### `WARDiffPathTracerParams`

路径追踪运行时参数（CPU/GPU 共享）。

| 字段 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `useFixedSeed` | `int` | false | 是否使用固定随机种子（调试用） |
| `fixedSeed` | `uint` | 1 | 固定种子值 |
| `assertThreshold` | `float` | 1e9 | NaN 断言阈值 |
| `runBackward` | `uint` | 1 | 运行时控制反向传播/原始渲染 |
| `frameDim` | `uint2` | {0,0} | 帧尺寸（像素） |
| `screenTiles` | `uint2` | {0,0} | 屏幕瓦片数 |
| `frameCount` | `uint` | 0 | 已渲染帧数（用作随机种子） |
| `seed` | `uint` | 0 | 当前随机种子 |

### 路径配置常量

| 常量 | 值 | 说明 |
|------|-----|------|
| `kMaxSamplesPerPixel` | 16 | 最大每像素采样数 |
| `kMaxFrameDimension` | 4096 | 最大帧尺寸 |
| `kBounceLimit` | 254 | 最大反弹次数限制 |
| `kMaxLightSamplesPerVertex` | 8 | 每个路径顶点最大光源采样数 |

## 依赖关系 / import

- `Utils/HostDeviceShared.slangh` — 主机/设备共享工具
- `StaticParams` — 静态编译时参数（GPU 端导出）
- `Utils.Math.BitTricks` — 位操作工具（GPU 端导出）

## 实现细节

- 使用 `#ifndef HOST_CODE` 条件编译区分 CPU 和 GPU 端代码，GPU 端额外导出 `StaticParams` 和 `BitTricks` 模块。
- 结构体布局遵循 HLSL 打包规则，使用 `int` 而非 `bool` 以确保跨平台内存对齐一致性。
