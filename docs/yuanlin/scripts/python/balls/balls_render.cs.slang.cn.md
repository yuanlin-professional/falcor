# balls_render.cs.slang 着色器文档

> 路径: `scripts/python/balls/balls_render.cs.slang`
> 类型: Slang 计算着色器
> 模块: scripts/python/balls

## 功能概述

该计算着色器负责将小球渲染到二维输出纹理上。着色器遍历所有小球，对于每个像素判断其是否位于某个小球的半径范围内，若在范围内则根据小球索引生成伪彩色并使用 `smoothstep` 实现边缘平滑过渡效果，最终将颜色写入输出纹理。线程组大小为 16x16x1，每个线程处理一个像素。

## 结构体与接口

### `Ball`

| 字段 | 类型 | 说明 |
|------|------|------|
| `pos` | `float2` | 小球在二维空间中的位置坐标（范围 [-1, 1]） |
| `vel` | `float2` | 小球的速度向量 |

## 全局资源

| 名称 | 类型 | 说明 |
|------|------|------|
| `g_balls` | `StructuredBuffer<Ball>` | 只读结构化缓冲区，存储所有小球的数据 |
| `g_ball_count` | `uniform uint` | 小球总数 |
| `g_ball_radius` | `uniform float` | 小球的渲染半径 |
| `g_output` | `RWTexture2D<float4>` | 可读写的二维输出纹理 |
| `g_resolution` | `uniform uint` | 输出纹理的分辨率（宽度与高度相同） |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `float3 pseudocolor(uint value)` | 根据输入整数值通过 Jenkins 哈希生成伪随机 RGB 颜色，用于区分不同小球 |
| `void main(uint3 thread_id: SV_DispatchThreadID)` | 计算着色器入口函数。将像素坐标映射到 [-1, 1] 的归一化空间，遍历所有小球并累加颜色贡献，写入输出纹理 |

## 依赖关系

### import

- `Utils.Math.HashUtils` -- 提供 `jenkinsHash` 哈希函数，用于伪彩色生成
