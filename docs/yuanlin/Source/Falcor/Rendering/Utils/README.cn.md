# Utils - 渲染工具

> 源码路径: `Source/Falcor/Rendering/Utils/`

## 功能概述

Utils 模块提供渲染管线的运行时统计和调试工具。当前主要包含 `PixelStats` 组件，用于收集路径追踪器中逐像素的光线统计信息（如各类光线数量、路径长度、体积查询次数等），支持 GPU 并行归约和异步 CPU 回读。

## 文件清单

| 文件名 | 类型 | 说明 |
|--------|------|------|
| `PixelStats.h` | C++ 头文件 | 像素统计收集器定义 |
| `PixelStats.cpp` | C++ 实现 | 统计收集、并行归约、CPU回读逻辑 |
| `PixelStats.slang` | Shader | GPU端统计数据记录接口 |
| `PixelStats.cs.slang` | Compute Shader | 统计数据归约计算着色器 |
| `PixelStatsShared.slang` | Shader | CPU/GPU共享的统计类型定义（PixelStatsRayType等） |

## 依赖关系

- **Core/**: `Buffer`, `Texture`, `Fence`, `ComputePass`, `Macros`
- **Utils/UI/**: `Gui`（统计信息UI显示）
- **Utils/Algorithm/**: `ParallelReduction`（GPU并行归约）

## 关键类与接口

### `PixelStats` (C++类)
路径追踪像素级统计收集器。

**统计指标** (`Stats` 结构体):
- `visibilityRays` / `avgVisibilityRays` - 可见性光线（阴影射线）总数/平均数
- `closestHitRays` / `avgClosestHitRays` - 最近命中光线总数/平均数
- `totalRays` / `avgTotalRays` - 总光线数/平均数
- `pathVertices` / `avgPathVertices` - 路径顶点总数/平均数
- `volumeLookups` / `avgVolumeLookups` - 体积查询总数/平均数
- `avgPathLength` - 平均路径长度

**使用流程**:
1. `beginFrame()` - 初始化帧统计缓冲区
2. `prepareProgram()` - 在渲染 shader 中绑定统计数据资源
3. shader 中通过 `PixelStats.slang` 接口记录光线事件
4. `endFrame()` - 触发 GPU 并行归约并启动异步回读
5. `getStats()` - 获取归约后的统计数据（可能延迟一帧）

**纹理输出**:
- `getRayCountTexture()` - 逐像素总光线数（R32Uint）
- `getPathLengthTexture()` - 逐像素路径长度
- `getPathVertexCountTexture()` - 逐像素路径顶点数
- `getVolumeLookupCountTexture()` - 逐像素体积查询数
