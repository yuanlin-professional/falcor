# MultiSampling.3d.slang 着色器文档

> 路径: `Source/Samples/MultiSampling/MultiSampling.3d.slang`
> 类型: Slang 着色器
> 模块: Samples/MultiSampling

## 功能概述

MultiSampling 示例的简单光栅化着色器。顶点着色器将 2D 位置直接传递为裁剪空间坐标,像素着色器输出固定的灰色值。该着色器仅用于演示多重采样渲染流程,不包含任何复杂的着色逻辑。

## 结构体与接口

### `VSIn`

| 成员 | 语义 | 类型 | 说明 |
|------|------|------|------|
| `pos` | `POSITION` | `float2` | 2D 顶点位置 |

### `VSOut`

| 成员 | 语义 | 类型 | 说明 |
|------|------|------|------|
| `pos` | `SV_POSITION` | `float4` | 裁剪空间位置 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `VSOut vsMain(VSIn vIn)` | 顶点着色器,将 2D 位置扩展为 `float4(pos, 0, 1)` |
| `float4 psMain(VSOut vsOut, uint triangleIndex: SV_PrimitiveID) : SV_TARGET` | 像素着色器,返回固定灰色 `float4(0.5)` |

## 依赖关系 / import

无外部依赖。

## 实现细节

- 顶点着色器直接将输入的 2D 坐标作为裁剪空间坐标使用(z=0, w=1),无需任何变换矩阵。
- 像素着色器输出恒定的中灰色,重点在于展示 MSAA 渲染管线而非着色效果。
