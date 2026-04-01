# PixelStats.slang 源码文档

> 路径: `Source/Falcor/Rendering/Utils/PixelStats.slang`
> 类型: Slang 着色器模块
> 模块: Rendering/Utils

## 功能概述

本文件提供了在路径追踪着色器中收集运行时统计信息的 GPU 端接口函数。通过宏 `_PIXEL_STATS_ENABLED` 控制是否实际执行统计操作。当禁用时，所有函数退化为空操作，不产生运行时开销。

## 结构体与接口

### 全局资源

| 资源 | 类型 | 说明 |
|------|------|------|
| `gStatsRayCount[]` | `RWTexture2D<uint>[Count]` | 各类型光线计数纹理数组 |
| `gStatsPathLength` | `RWTexture2D<uint>` | 路径长度纹理 |
| `gStatsPathVertexCount` | `RWTexture2D<uint>` | 路径顶点计数纹理 |
| `gStatsVolumeLookupCount` | `RWTexture2D<uint>` | 体积查询计数纹理 |

### 静态变量

| 变量 | 类型 | 说明 |
|------|------|------|
| `gPixelStatsPixel` | `uint2` | 当前像素坐标（仅启用时存在） |

## 函数

| 函数 | 说明 |
|------|------|
| `logSetPixel(uint2 pixel)` | 设置当前统计像素坐标 |
| `logTraceRay(PixelStatsRayType rayType)` | 记录一次光线追踪（原子加 1） |
| `logPathLength(uint pathLength)` | 记录路径长度（直接写入） |
| `logPathVertex()` | 记录一个路径顶点（原子加 1） |
| `logVolumeLookup()` | 记录一次体积查询（原子加 1） |

## 依赖关系 / import

- `PixelStatsShared`（`__exported import`，导出 `PixelStatsRayType` 枚举）

## 实现细节

- `logTraceRay` 和 `logPathVertex`、`logVolumeLookup` 使用 `InterlockedAdd` 确保多线程安全
- `logPathLength` 使用直接写入（每个像素只设置一次路径长度）
- 调用方需先调用 `logSetPixel()` 设置当前像素，后续所有 log 操作使用该像素坐标
- 宏控制使调试构建无额外开销
