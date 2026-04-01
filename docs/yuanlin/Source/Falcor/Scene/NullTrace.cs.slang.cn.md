# NullTrace.cs.slang 源码文档

> 路径: `Source/Falcor/Scene/NullTrace.cs.slang`
> 类型: Compute Shader（Slang 计算着色器）
> 模块: Scene

## 功能概述

NullTrace 是一个最小化的 GPU 计算着色器，用于执行简单的内联光线追踪（Inline Ray Tracing / DXR 1.1 RayQuery）测试。它从固定的原点向固定方向发射一条光线，判断是否命中三角形，并将结果（0 或 1）写入输出纹理。

此着色器主要用于验证光线追踪加速结构（TLAS）的正确性，或作为内联光线追踪的最小示例。

## 结构体与接口

无自定义结构体。

## 函数

| 入口点 | 说明 |
|--------|------|
| `[numthreads(16,16,1)] main` | 发射固定光线并将命中结果写入输出纹理 |

## 关键资源

| 资源 | 类型 | 说明 |
|------|------|------|
| `gTlas` | `[root] RaytracingAccelerationStructure` | 顶层加速结构 |
| `gOutput` | `RWTexture2D<uint>` | 输出纹理，存储命中结果 |

## 依赖关系 / import

- `Utils.Attributes` - 着色器属性工具

## 实现细节

- 使用 `RAY_FLAG_FORCE_OPAQUE | RAY_FLAG_SKIP_PROCEDURAL_PRIMITIVES` 标志，仅测试不透明三角形
- 光线参数固定：原点 (0,0,0)，方向 (1,0,0)，范围 [0, 1]
