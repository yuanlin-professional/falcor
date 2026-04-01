# BSDFTests 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Scene/Material/BSDFTests.cpp`
> 类型: C++ 实现
> 模块: Tools/FalcorTest/Tests/Scene/Material

## 功能概述

测试 BSDF 材质模型的评估、采样和 PDF 计算，验证各标准材质的光散射行为。

## 结构体

- `BsdfConfig`: 数据结构定义
- `SamplingTestSpec`: 数据结构定义


## 测试用例

### GPU 测试

| 测试名称 | 说明 |
|----------|------|
| `TestBsdf_DisneyDiffuseBRDF` | GPU 测试用例 |
| `TestBsdf_FrostbiteDiffuseBRDF` | GPU 测试用例 |
| `TestBsdf_LambertDiffuseBRDF` | GPU 测试用例 |
| `TestBsdf_LambertDiffuseBTDF` | GPU 测试用例 |
| `TestBsdf_OrenNayarBRDF` | GPU 测试用例 |
| `TestBsdf_SpecularMicrofacetBRDF` | GPU 测试用例 |
| `TestBsdf_SheenBSDF` | GPU 测试用例 |
| `TestBsdf_DiffuseSpecularBRDF` | GPU 测试用例 |


## 依赖关系

### 本文件引用

- `Testing/UnitTest.h`
- `hypothesis/hypothesis.h`
- `random`
- `fstream`
- `iostream`


### 关联着色器

- [`BSDFTests.cs.slang`](BSDFTests.cs.slang.md)

## 实现细节

本文件属于 Falcor 测试框架的一部分，使用 `Testing/UnitTest.h` 提供的 `GPU_TEST` 和 `CPU_TEST` 宏定义测试用例。测试通过 `GPUUnitTestContext` 上下文对象与 GPU 设备交互，执行计算着色器并验证输出结果。
