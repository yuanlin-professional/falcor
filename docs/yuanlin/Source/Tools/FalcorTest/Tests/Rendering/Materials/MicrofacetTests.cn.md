# MicrofacetTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Rendering/Materials/MicrofacetTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Rendering/Materials

## 功能概述

测试微表面（Microfacet）BRDF 模型的评估和采样，验证 GGX 等法线分布函数的正确性。

## 结构体

- `NDFConfig`: 数据结构定义
- `SamplingTestSpec`: 数据结构定义


## 测试用例

### GPU 测试

| 测试名称 | 说明 |
|----------|------|
| `MicrofacetSampling` | GPU 测试用例 |
| `MicrofacetSigmaIntegration` | GPU 测试用例 |
| `MicrofacetSigmaLambdaConsistency` | GPU 测试用例 |
| `MicrofacetLambdaNonsymmetry` | GPU 测试用例 |
| `MicrofacetG1Symmetry` | GPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `hypothesis/hypothesis.h`
- `iostream`


### 关联着色器

- [`MicrofacetTests.cs.slang`](MicrofacetTests.cs.slang.md)

## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
