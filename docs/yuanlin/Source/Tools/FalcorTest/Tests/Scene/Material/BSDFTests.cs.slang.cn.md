# BSDFTests.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Scene/Material/BSDFTests.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Scene/Material

## 功能概述

测试 BSDF 材质模型的评估、采样和 PDF 计算，验证各标准材质的光散射行为。

## 结构体与接口

### `SamplingTest`

结构体定义。


## 常量缓冲区

- `SamplingTestCB`: 常量缓冲区


## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `testWi` | `StructuredBuffer` | 着色器资源 |
| `testParams` | `StructuredBuffer` | 着色器资源 |
| `histogramSampling` | `RWStructuredBuffer` | 着色器资源 |
| `histogramPdf` | `RWStructuredBuffer` | 着色器资源 |
| `histogramWeightError` | `RWStructuredBuffer` | 着色器资源 |
| `histogramPdfError` | `RWStructuredBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `tabulateHistogram` | [256, 1, 1] | 计算着色器入口点 |
| `tabulatePdf` | [16, 16, 1] | 计算着色器入口点 |
| `tabulateWeightAndPdfError` | [256, 1, 1] | 计算着色器入口点 |


## 依赖关系 / import

- `Utils.Math.MathHelpers`
- `Utils.Color.ColorHelpers`
- `Utils.Sampling.UniformSampleGenerator`
- `TEST_BSDF_IMPORT`


### 关联 C++ 测试

- [`BSDFTests.cpp`](BSDFTests.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
