# UniformSampleGenerator.slang 源码文档

> 路径: `Source/Falcor/Utils/Sampling/UniformSampleGenerator.slang`
> 类型: Slang 着色器文件
> 模块: Utils/Sampling

## 功能概述

实现了 **默认的均匀伪随机数生成器**，基于 Xoshiro128** 算法，使用 128 位状态。该生成器具有良好的统计特性，适合大多数渲染应用。需要 Shader Model 6.0 或更高版本。

## 结构体与接口

### `UniformSampleGenerator`

- **实现接口**: `ISampleGenerator`
- **职责**: 提供基于 Xoshiro128** 的高质量 128 位伪随机数生成

| 成员 | 类型 | 说明 |
|------|------|------|
| `rng` | `Xoshiro128StarStar` | Xoshiro128** 伪随机数生成器状态（128 位） |

### `UniformSampleGenerator::Padded`

填充版本。由于 Xoshiro128** 状态本身为 128 位（16 字节），已是 16 字节对齐，无需额外填充。

| 成员 | 类型 | 说明 |
|------|------|------|
| `internal` | `UniformSampleGenerator` | 内部采样器实例 |

## 函数

| 函数签名 | 说明 |
|----------|------|
| `__init(uint2 pixel, uint sampleNumber)` | 根据像素坐标和采样编号初始化。使用 SplitMix64 生成器产生高质量的 128 位初始状态 |
| `[mutating] uint next()` | 返回下一个 32 位伪随机数，同时更新 Xoshiro128** 状态 |

## 依赖关系 / import

- `Utils.Sampling.SampleGeneratorInterface` — `ISampleGenerator` 接口定义（`__exported import`）
- `Utils.Sampling.Pseudorandom.Xoshiro` — Xoshiro128** 伪随机数生成器
- `Utils.Sampling.Pseudorandom.SplitMix64` — SplitMix64 种子初始化器
- `Utils.Math.BitTricks` — 位操作工具（`interleave_32bit`）

## 实现细节

- **种子初始化策略**：
  1. 像素坐标通过 `interleave_32bit` 交错为 32 位值
  2. 与采样编号一起作为 SplitMix64 的种子
  3. 从 SplitMix64 生成两个 64 位值（共 128 位）作为 Xoshiro128** 的初始状态
- **唯一性保证**：像素坐标预期最大 28 位（D3D12 资源限制为 16K^2），采样编号预期最大约 28 位。只要两者均不超过 32 位，初始种子就是唯一的
- **重叠概率分析**：在最极端参数下（2^56 条序列，每条长度 L），序列重叠的概率为 P(overlap) = L * 2^-16
- Xoshiro128** 周期为 2^128 - 1，远大于实际渲染所需
- 标记为 `export struct`，可被其他 Slang 模块导入使用
- 这是 Falcor 的 **默认采样生成器**（`SAMPLE_GENERATOR_DEFAULT`）
