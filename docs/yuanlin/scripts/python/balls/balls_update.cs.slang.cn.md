# balls_update.cs.slang 着色器文档

> 路径: `scripts/python/balls/balls_update.cs.slang`
> 类型: Slang 计算着色器
> 模块: scripts/python/balls

## 功能概述

该计算着色器负责更新小球的物理状态。每个线程处理一个小球，根据速度和时间步长更新其位置，并在小球超出 [-1, 1] 的边界范围时进行反弹处理（位置钳制到边界，速度分量取反）。线程组大小为 128x1x1，适用于一维调度。

## 结构体与接口

### `Ball`

| 字段 | 类型 | 说明 |
|------|------|------|
| `pos` | `float2` | 小球在二维空间中的位置坐标 |
| `vel` | `float2` | 小球的速度向量 |

## 全局资源

| 名称 | 类型 | 说明 |
|------|------|------|
| `g_balls` | `RWStructuredBuffer<Ball>` | 可读写结构化缓冲区，存储所有小球的数据 |
| `g_ball_count` | `uniform uint` | 小球总数 |
| `g_dt` | `uniform float` | 时间步长（每帧的时间增量） |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void main(uint3 thread_id: SV_DispatchThreadID)` | 计算着色器入口函数。根据 `thread_id.x` 索引获取对应小球，按 `pos += vel * dt` 更新位置，检测并处理 X/Y 方向的边界反弹 |

## 依赖关系

### import

无外部依赖。该着色器是自包含的，不引入任何外部模块。
