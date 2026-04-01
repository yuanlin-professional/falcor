# LCG.slang 着色器文档

> 路径: `Source/Falcor/Utils/Sampling/Pseudorandom/LCG.slang`
> 类型: Slang 着色器
> 模块: Utils/Sampling/Pseudorandom

## 功能概述

实现线性同余生成器（Linear Congruential Generator, LCG），这是一种简单的伪随机数生成器。使用《Numerical Recipes》系列书籍中的参数，周期为 2^32，状态大小为 32 位。

**注意**：仅适用于基础应用。该生成器统计特性较差，对种子质量敏感。如果使用大量并行生成器（例如每像素一个），生成的伪随机序列之间会存在显著相关性。在这些情况下，建议使用状态更大的生成器。

## 结构体与接口

### `LCG`
| 字段 | 类型 | 说明 |
|------|------|------|
| `state` | `uint` | 生成器的内部状态（32 位） |

## 函数

| 函数签名 | 说明 |
|---------|------|
| `uint nextRandom(inout LCG rng)` | 生成序列中的下一个伪随机数（32 位），使用参数 A=1664525, C=1013904223 |
| `LCG createLCG(uint s0)` | 初始化 LCG 伪随机数生成器，s0 为初始状态（种子） |

## 依赖关系

### import
无外部依赖
