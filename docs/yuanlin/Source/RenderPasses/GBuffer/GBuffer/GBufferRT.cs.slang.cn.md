# GBufferRT.cs.slang 源码文档

> 路径: `Source/RenderPasses/GBuffer/GBuffer/GBufferRT.cs.slang`
> 类型: Slang 计算着色器
> 模块: RenderPasses/GBuffer

## 功能概述

使用内联光线追踪（TraceRayInline/SceneRayQuery）的 G-buffer 生成计算着色器。以 16x16 线程组调度，每个线程处理一个像素。通过 `SceneRayQuery` 执行光线查询，根据命中/未命中结果调用 `GBufferRT` 的相应写入函数。

## 函数

| 函数 | 说明 |
|------|------|
| `main(uint3 dispatchThreadId)` | 计算着色器入口。生成光线、执行场景查询、写入 G-buffer 数据 |

## 依赖关系 / import

- `Scene.RaytracingInline` — 内联光线追踪
- `GBufferRT` — G-buffer 核心逻辑
