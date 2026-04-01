# AggregateGradients.cs.slang 源码文档

> 路径: `Source/Falcor/DiffRendering/AggregateGradients.cs.slang`
> 类型: 计算着色器 (Compute Shader)
> 模块: 可微渲染 (DiffRendering)

## 功能概述

本文件实现了梯度聚合的计算着色器。在可微渲染流程中，多个像素可能对同一场景参数产生梯度贡献，这些梯度首先被写入到临时缓冲区（使用哈希网格避免冲突），然后通过本着色器聚合到最终的梯度缓冲区中。支持两种聚合模式：直接模式（Direct）和哈希网格模式（HashGrid）。

## 结构体与接口

### `GradientsAggregator`

- **职责**: 封装梯度聚合逻辑，从临时梯度缓冲区读取数据并聚合到最终梯度缓冲区

#### 关键成员

| 成员 | 类型 | 说明 |
|------|------|------|
| `gradDim` | `uint` | 梯度维度（参数总数） |
| `hashSize` | `uint` | 哈希表大小 |
| `tmpGrads` | `ByteAddressBuffer` | 临时梯度缓冲区（只读） |
| `grads` | `RWByteAddressBuffer` | 最终梯度缓冲区（读写） |

#### 关键方法

| 方法签名 | 说明 |
|----------|------|
| `void aggregateDirect(uint2 threadID)` | 直接模式聚合：直接将临时缓冲区的值存储到最终缓冲区，适用于无冲突场景 |
| `void aggregateHashGrid(uint2 threadID)` | 哈希网格模式聚合：使用原子加操作将哈希表中多个槽位的梯度累加到最终缓冲区 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `[numthreads(256,1,1)] void mainDirect(uint3 dispatchThreadID)` | 直接聚合模式的计算着色器入口点 |
| `[numthreads(64,8,1)] void mainHashGrid(uint3 dispatchThreadID)` | 哈希网格聚合模式的计算着色器入口点 |

## 依赖关系

### import

| 模块/文件 | 说明 |
|-----------|------|
| `Utils/NVAPI.slangh` | NVIDIA API 扩展（用于 `InterlockedAddF32` 等原子浮点操作） |
| `DiffRendering.SharedTypes` | 可微渲染共享类型定义 |

### 被以下文件引用

- `Source/Falcor/DiffRendering/SceneGradients.cpp` — 在 C++ 端创建并调度此计算着色器
