# BSDFOptimizerParams.slang 源码文档

> 路径: `Source/RenderPasses/BSDFOptimizer/BSDFOptimizerParams.slang`
> 类型: Slang 着色器头文件
> 模块: RenderPasses/BSDFOptimizer

## 功能概述

定义 BSDFOptimizer 在主机和设备之间共享的参数结构体。控制三视口布局、双向散射分布函数切片表维度和材质 ID 配置。

## 结构体与接口

### `BSDFOptimizerParams`

| 字段 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `frameDim` | `uint2` | {0,0} | 帧尺寸 |
| `frameCount` | `uint` | 0 | 帧计数 |
| `initViewPortOffset` | `float2` | — | 初始材质视口偏移 |
| `diffViewPortOffset` | `float2` | — | 差异视口偏移 |
| `refViewPortOffset` | `float2` | — | 参考材质视口偏移 |
| `viewPortScale` | `float2` | — | 视口缩放 |
| `bsdfTableDim` | `uint2` | {0,0} | 双向散射分布函数切片表维度 |
| `initMaterialID` | `uint` | 0 | 初始（待优化）材质 ID |
| `refMaterialID` | `uint` | 1 | 参考（目标）材质 ID |

## 依赖关系 / import

- `Utils/HostDeviceShared.slangh` — 主机/设备共享工具

## 实现细节

- 结构体遵循 HLSL 打包规则，确保 CPU/GPU 端布局一致。
- 三个视口偏移定义了帧中三个并排显示区域的位置。
