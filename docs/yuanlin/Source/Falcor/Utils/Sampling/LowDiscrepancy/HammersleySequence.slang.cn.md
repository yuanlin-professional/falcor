# HammersleySequence.slang 着色器文档

> 路径: `Source/Falcor/Utils/Sampling/LowDiscrepancy/HammersleySequence.slang`
> 类型: Slang 着色器
> 模块: Utils/Sampling/LowDiscrepancy

## 功能概述

实现 Hammersley 序列生成器，这是一种低差异序列（Low-Discrepancy Sequence），用于生成均匀分布的二维采样点。Hammersley 序列在蒙特卡洛积分、准蒙特卡洛方法和重要性采样中广泛应用，相比伪随机数具有更好的分布均匀性。

## 结构体与接口

无结构体定义，仅提供函数接口。

## 函数

| 函数签名 | 说明 |
|---------|------|
| `float radicalInverse(uint i)` | 计算基数为 2 的 radical inverse 函数，通过位反转算法将整数映射到 [0,1) 区间 |
| `float2 getHammersley(uint i, uint N)` | 生成 Hammersley 序列的第 i 个二维采样点，N 为序列总长度。返回值 x 分量为 i/N，y 分量为 radicalInverse(i) |

## 依赖关系

### import
无外部依赖
