# Xorshift32.slang 着色器文档

> 路径: `Source/Falcor/Utils/Sampling/Pseudorandom/Xorshift32.slang`
> 类型: Slang 着色器
> 模块: Utils/Sampling/Pseudorandom

## 功能概述

实现 Xorshift32 伪随机数生成器，这是一个体积小巧的伪随机生成器，相对于其大小具有相当好的特性。但由于其状态较小，对于许多使用场景仍然不够理想。

**建议**：优先使用具有更大状态的伪随机数生成器。

## 结构体与接口

### `Xorshift32`
| 字段 | 类型 | 说明 |
|------|------|------|
| `state` | `uint` | 生成器的内部状态（32 位） |

## 函数

| 函数签名 | 说明 |
|---------|------|
| `__init(uint seed)` | 构造函数，使用种子初始化 Xorshift32 伪随机数生成器 |
| `[mutating] uint next()` | 生成序列中的下一个伪随机数（32 位），该函数会更新内部状态 |

## 依赖关系

### import
无外部依赖
