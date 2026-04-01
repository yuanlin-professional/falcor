# CrossFade.cs.slang 源码文档

> 路径: `Source/RenderPasses/Utils/CrossFade/CrossFade.cs.slang`
> 类型: 计算着色器
> 模块: RenderPasses/Utils/CrossFade

## 功能概述

此着色器实现了交叉淡入淡出的 GPU 逻辑。对每个像素执行加权混合：`output = scaleA * A + scaleB * B`。

## 结构体与接口

### 常量缓冲区 `CB`

| 字段 | 类型 | 说明 |
|------|------|------|
| `frameDim` | `uint2` | 帧尺寸 |
| `scaleA` | `float` | 输入 A 的权重 (1-t) |
| `scaleB` | `float` | 输入 B 的权重 (t) |

### 资源绑定

| 名称 | 类型 | 说明 |
|------|------|------|
| `A` | `Texture2D<float4>` | 输入纹理 A |
| `B` | `Texture2D<float4>` | 输入纹理 B |
| `output` | `RWTexture2D<float4>` | 输出纹理 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `void main(uint3 dispatchThreadId)` | 计算着色器入口，线程组 [16,16,1] |

## 依赖关系 / import

- 无外部依赖
