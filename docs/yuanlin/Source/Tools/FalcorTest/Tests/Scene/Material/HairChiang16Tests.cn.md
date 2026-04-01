# HairChiang16Tests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Scene/Material/HairChiang16Tests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Scene/Material

## 功能概述

测试 Chiang 2016 头发散射模型的 BSDF 评估和采样，验证多级散射分量的正确性。

## 测试用例

### GPU 测试

| 测试名称 | 说明 |
|----------|------|
| `HairChiang16_PbrtReference` | GPU 测试用例 |
| `HairChiang16_WhiteFurnaceUniform` | GPU 测试用例 |
| `HairChiang16_WhiteFurnaceImportanceSampling` | GPU 测试用例 |
| `HairChiang16_ImportanceSamplingWeights` | GPU 测试用例 |
| `HairChiang16_SamplingConsistency` | GPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `Utils/Sampling/SampleGenerator.h`
- `random`
- `fstream`
- `iostream`


### 关联着色器

- [`HairChiang16Tests.cs.slang`](HairChiang16Tests.cs.slang.md)

## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
