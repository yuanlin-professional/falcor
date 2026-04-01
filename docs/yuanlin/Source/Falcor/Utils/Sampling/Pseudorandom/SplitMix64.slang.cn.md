# SplitMix64.slang 着色器文档

> 路径: `Source/Falcor/Utils/Sampling/Pseudorandom/SplitMix64.slang`
> 类型: Slang 着色器
> 模块: Utils/Sampling/Pseudorandom

## 功能概述

实现 SplitMix64 伪随机数生成器，这是 Java 8 的 SplittableRandom 生成器的固定增量版本。周期为 2^64，状态大小为 64 位。这是一个非常快速的生成器，通过了 BigCrush 测试。推荐用于初始化其他生成器（如 xoroshiro 和 xorshift）的状态数组。

**要求**：需要 Shader Model 6.0 或更高版本以支持 64 位整数。

**参考文献**：Steele Jr, Guy L., Doug Lea, and Christine H. Flood., "Fast Splittable Pseudorandom Number Generators", ACM SIGPLAN Notices 49.10 (2014): 453-472.

## 结构体与接口

### `SplitMix64`
| 字段 | 类型 | 说明 |
|------|------|------|
| `state` | `uint64_t` | 生成器的内部状态（64 位） |

## 函数

| 函数签名 | 说明 |
|---------|------|
| `uint64_t asuint64(uint lowbits, uint highbits)` | 将两个 32 位整数组合成一个 64 位整数 |
| `uint64_t nextRandom64(inout SplitMix64 rng)` | 生成序列中的下一个伪随机数（64 位） |
| `uint nextRandom(inout SplitMix64 rng)` | 生成序列中的下一个伪随机数（低 32 位） |
| `SplitMix64 createSplitMix64(uint s0, uint s1)` | 初始化 SplitMix64 伪随机数生成器，s0 为初始状态的低位，s1 为高位 |

## 依赖关系

### import
无外部依赖
