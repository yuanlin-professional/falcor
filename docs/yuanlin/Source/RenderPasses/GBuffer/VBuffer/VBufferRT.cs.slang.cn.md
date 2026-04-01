# VBufferRT.cs.slang 源码文档

> 路径: `Source/RenderPasses/GBuffer/VBuffer/VBufferRT.cs.slang`
> 类型: Slang 计算着色器
> 模块: RenderPasses/GBuffer/VBuffer

## 功能概述

使用内联光线追踪的 V-buffer 生成计算着色器。以 16x16 线程组调度，每线程处理一个像素。通过 `SceneRayQuery` 执行光线查询并写入 V-buffer。

## 函数

| 函数 | 说明 |
|------|------|
| `main(uint3 dispatchThreadId)` | 计算着色器入口，生成光线、查询场景、写入 V-buffer |

## 依赖关系 / import

- `Scene.RaytracingInline` — 内联光线追踪
- `VBufferRT` — V-buffer 核心逻辑
