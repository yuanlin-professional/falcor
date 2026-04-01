# TinyUniformSampleGenerator.slang 源码文档

> 路径: `Source/Falcor/Utils/Sampling/TinyUniformSampleGenerator.slang`
> 类型: Slang 着色器文件
> 模块: Utils/Sampling

## 功能概述

实现了 **轻量级 32 位均匀随机采样生成器**。基于线性同余生成器（LCG），仅使用 32 位状态。该生成器统计特性较差，不适合关键性采样任务，可能出现明显的相关性伪影，但因其极低的存储和计算开销适合对质量要求不高的场景。

## 结构体与接口

### `TinyUniformSampleGenerator`

- **实现接口**: `ISampleGenerator`
- **职责**: 提供基于 LCG 的 32 位伪随机数生成

| 成员 | 类型 | 说明 |
|------|------|------|
| `rng` | `LCG` | LCG 32 位伪随机数生成器状态 |

### `TinyUniformSampleGenerator::Padded`

填充版本，使结构体对齐到 16 字节倍数。

| 成员 | 类型 | 说明 |
|------|------|------|
| `internal` | `TinyUniformSampleGenerator` | 内部采样器实例 |
| `_pad` | `uint3` | 填充至 16 字节对齐 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `__init(uint seed)` | 使用给定种子初始化 LCG 生成器 |
| `__init(uint2 pixel, uint sampleNumber)` | 根据像素坐标和采样编号初始化。使用 TEA 分组密码生成伪随机种子，先对像素坐标进行 32 位交错（interleave），再与采样编号组合 |
| `[mutating] uint next()` | 返回下一个 32 位伪随机数，同时更新 LCG 状态 |

## 依赖关系 / import

- `Utils.Sampling.SampleGeneratorInterface` — `ISampleGenerator` 接口定义（`__exported import`）
- `Utils.Sampling.Pseudorandom.LCG` — 线性同余生成器实现
- `Utils.Math.HashUtils` — 分组密码 TEA（`blockCipherTEA`）
- `Utils.Math.BitTricks` — 位操作工具（`interleave_32bit`）

## 实现细节

- 像素坐标到种子的映射使用 `interleave_32bit` 将 2D 坐标交错为 1D 值，然后通过 TEA 分组密码与采样编号混合，确保不同像素和不同采样编号得到不同的初始种子
- LCG 的周期为 2^32，状态仅占 32 位（4 字节），非常节省寄存器
- 标记为 `export struct`，可被其他 Slang 模块导入使用
- 适用场景：对采样质量要求较低的预览渲染、调试等
