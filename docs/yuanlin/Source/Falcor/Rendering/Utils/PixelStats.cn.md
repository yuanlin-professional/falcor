# PixelStats 源码文档

> 路径: `Source/Falcor/Rendering/Utils/PixelStats.h` + `Source/Falcor/Rendering/Utils/PixelStats.cpp`
> 类型: C++ 头文件 + 实现
> 模块: Rendering/Utils

## 功能概述

`PixelStats` 是一个辅助类，用于在路径追踪器中收集运行时逐像素统计信息。它在 GPU 上记录每像素的光线计数、路径长度、路径顶点数和体积查询次数，并通过并行归约将统计结果异步回读到 CPU。

工作流程：
1. `beginFrame()` — 清空统计缓冲区
2. `prepareProgram()` — 为着色器添加 `_PIXEL_STATS_ENABLED` 宏并绑定资源
3. 着色器中调用 `logTraceRay()`、`logPathLength()` 等函数记录统计
4. `endFrame()` — 启动并行归约和异步回读
5. `getStats()` / `renderUI()` — 获取或显示汇总结果

## 类与接口

### `PixelStats`

- **继承**: 无
- **职责**: 管理逐像素统计缓冲区的创建、清零、归约和 CPU 回读

#### 内部结构体 `Stats`

| 成员 | 类型 | 说明 |
|------|------|------|
| `visibilityRays` | `uint32_t` | 可见性光线总数 |
| `closestHitRays` | `uint32_t` | 最近命中光线总数 |
| `totalRays` | `uint32_t` | 总光线数 |
| `pathVertices` | `uint32_t` | 路径顶点总数 |
| `volumeLookups` | `uint32_t` | 体积查询总数 |
| `avgVisibilityRays` | `float` | 每像素平均可见性光线数 |
| `avgClosestHitRays` | `float` | 每像素平均最近命中光线数 |
| `avgTotalRays` | `float` | 每像素平均总光线数 |
| `avgPathLength` | `float` | 每像素平均路径长度 |
| `avgPathVertices` | `float` | 每像素平均路径顶点数 |
| `avgVolumeLookups` | `float` | 每像素平均体积查询数 |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `PixelStats(ref<Device>)` | 构造函数，创建总光线计数的计算通道 |
| `void setEnabled(bool)` | 启用/禁用统计收集 |
| `bool isEnabled() const` | 查询是否启用 |
| `void beginFrame(RenderContext*, const uint2&)` | 帧开始，分配/清空纹理 |
| `void endFrame(RenderContext*)` | 帧结束，执行并行归约并异步回读 |
| `void prepareProgram(const ref<Program>&, const ShaderVar&)` | 着色器特化和资源绑定 |
| `void renderUI(Gui::Widgets&)` | 渲染统计 UI |
| `bool getStats(Stats&)` | 获取统计结果（等待回读完成） |
| `const ref<Texture> getRayCountTexture(RenderContext*)` | 获取总光线计数纹理（按需计算） |
| `const ref<Texture> getPathLengthTexture() const` | 获取路径长度纹理 |
| `const ref<Texture> getPathVertexCountTexture() const` | 获取路径顶点计数纹理 |
| `const ref<Texture> getVolumeLookupCountTexture() const` | 获取体积查询计数纹理 |

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `mpParallelReduction` | `unique_ptr<ParallelReduction>` | GPU 并行归约辅助器 |
| `mpReductionResult` | `ref<Buffer>` | 回读结果缓冲区（CPU 可映射） |
| `mpFence` | `ref<Fence>` | GPU 围栏，用于同步异步回读 |
| `mpStatsRayCount[]` | `ref<Texture>[kRayTypeCount]` | 各类型光线计数纹理（R32Uint） |
| `mpStatsPathLength` | `ref<Texture>` | 路径长度纹理（R32Uint） |
| `mpStatsPathVertexCount` | `ref<Texture>` | 路径顶点计数纹理 |
| `mpStatsVolumeLookupCount` | `ref<Texture>` | 体积查询计数纹理 |
| `mpStatsRayCountTotal` | `ref<Texture>` | 总光线计数纹理（按需生成） |

## 依赖关系

### 本文件引用

- `PixelStatsShared.slang`（共享枚举 `PixelStatsRayType`）
- `Core/Macros.h`, `Core/API/Buffer.h`, `Core/API/Texture.h`, `Core/API/Fence.h`
- `Core/Pass/ComputePass.h`, `Core/API/RenderContext.h`
- `Utils/UI/Gui.h`, `Utils/Logger.h`
- `Utils/Algorithm/ParallelReduction.h`
- `Utils/Scripting/ScriptBindings.h`（Python 绑定）
- `Rendering/Utils/PixelStats.cs.slang`（总光线计数计算着色器）

### 被以下文件引用

- 路径追踪器（PathTracer）及其他需要运行时统计的渲染器

## 实现细节

- 使用 `R32Uint` 纹理存储逐像素统计，着色器中通过 `InterlockedAdd` 原子操作累加
- 并行归约分多次执行：2 次光线类型分别归约 + 3 次（路径长度、顶点数、体积查询）
- 异步回读使用 Fence 信号机制：`endFrame()` 提交命令并插入信号，`copyStatsToCPU()` 等待信号后映射缓冲区
- 提供 Python 脚本绑定，允许通过 `PixelStats.enabled` 和 `PixelStats.stats` 访问
- 总光线计数纹理通过 `PixelStats.cs.slang` 计算着色器按需生成
