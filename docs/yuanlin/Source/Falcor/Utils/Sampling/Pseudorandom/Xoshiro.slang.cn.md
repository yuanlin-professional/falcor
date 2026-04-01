# Xoshiro.slang 着色器文档

> 路径: `Source/Falcor/Utils/Sampling/Pseudorandom/Xoshiro.slang`
> 类型: Slang 着色器
> 模块: Utils/Sampling/Pseudorandom

## 功能概述

实现 xoshiro128** 32 位通用、稳定可靠的伪随机数生成器，由 David Blackman 和 Sebastiano Vigna 于 2018 年编写。状态为 128 位，周期为 (2^128)-1。提供跳跃函数，允许在序列中向前跳过 2^64 步。

**注意**：状态必须被种子化，使其不是全零。建议使用 SplitMix64 初始化状态。

**参考**：原始公共域代码 http://xoshiro.di.unimi.it/xoshiro128starstar.c

## 结构体与接口

### `Xoshiro128StarStar`
| 字段 | 类型 | 说明 |
|------|------|------|
| `state[4]` | `uint[4]` | 生成器的内部状态（4 个 32 位整数，共 128 位） |

## 函数

| 函数签名 | 说明 |
|---------|------|
| `uint rotl(const uint x, int k)` | 循环左移辅助函数，将 x 循环左移 k 位 |
| `uint nextRandom(inout Xoshiro128StarStar rng)` | 生成序列中的下一个伪随机数（32 位） |
| `void jump(inout Xoshiro128StarStar rng)` | 跳跃函数，等效于调用 2^64 次 nextRandom()，可用于为并行计算生成 2^64 个不重叠的子序列 |
| `Xoshiro128StarStar createXoshiro128StarStar(uint s[4])` | 初始化 Xoshiro128StarStar 伪随机数生成器，s 为包含 4 个 32 位值的初始状态数组（种子） |

## 依赖关系

### import
无外部依赖
