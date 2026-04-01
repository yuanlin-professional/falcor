# SampleGenerator.slang 源码文档

> 路径: `Source/Falcor/Utils/Sampling/SampleGenerator.slang`
> 类型: Slang 着色器文件
> 模块: Utils/Sampling

## 功能概述

GPU 端 **采样生成器类型选择** 的核心分发文件。根据主机端设置的 `SAMPLE_GENERATOR_TYPE` 宏定义，通过条件编译选择具体的采样器实现，并将其 typedef 为统一的 `SampleGenerator` 类型。

所有采样生成器遵循相同的 `ISampleGenerator` 接口，但 `SampleGenerator` 类型的实际大小可能因具体实现的状态大小而异。

## 结构体与接口

### 类型别名

根据 `SAMPLE_GENERATOR_TYPE` 宏的值，`SampleGenerator` 被定义为以下类型之一：

| 宏值 | 类型别名 | 对应实现 |
|------|----------|----------|
| `SAMPLE_GENERATOR_TINY_UNIFORM` (0) | `TinyUniformSampleGenerator` | 32 位轻量级 LCG 生成器 |
| `SAMPLE_GENERATOR_UNIFORM` (1) | `UniformSampleGenerator` | 128 位 Xoshiro128** 生成器 |

### `SampleGenerator.Padded`

每个采样器实现内部都定义了 `Padded` 子结构体，包含采样器实例加上额外填充，使结构体大小为 16 字节的倍数。

## 函数

无独立函数定义，仅进行类型选择。

## 依赖关系 / import

- `Utils/Sampling/SampleGeneratorType.slangh` — 采样器类型常量 (`#include`)
- `Utils/Sampling/SampleGeneratorInterface` — 采样器接口定义 (`__exported import`)
- `Utils/Sampling/TinyUniformSampleGenerator` — 条件导入
- `Utils/Sampling/UniformSampleGenerator` — 条件导入

## 实现细节

- 如果 `SAMPLE_GENERATOR_TYPE` 未定义，则不会定义 `SampleGenerator` 类型，后续使用会导致编译错误
- 使用 `__exported import` 确保 `SampleGeneratorInterface` 的内容对导入本模块的着色器可见
- 此文件是主机端 `SampleGenerator` 类（C++）与 GPU 端具体采样器实现之间的桥梁
