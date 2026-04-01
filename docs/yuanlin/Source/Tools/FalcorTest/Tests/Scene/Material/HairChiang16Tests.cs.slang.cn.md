# HairChiang16Tests.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Scene/Material/HairChiang16Tests.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Scene/Material

## 功能概述

测试 Chiang 2016 头发散射模型的 BSDF 评估和采样，验证多级散射分量的正确性。

## 常量缓冲区

- `TestCB`: 常量缓冲区


## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `gBetaM` | `StructuredBuffer` | 着色器资源 |
| `gBetaN` | `StructuredBuffer` | 着色器资源 |
| `gAlpha` | `StructuredBuffer` | 着色器资源 |
| `gIoR` | `StructuredBuffer` | 着色器资源 |
| `gSigmaA` | `StructuredBuffer` | 着色器资源 |
| `gH` | `StructuredBuffer` | 着色器资源 |
| `gWi` | `StructuredBuffer` | 着色器资源 |
| `gWo` | `StructuredBuffer` | 着色器资源 |
| `gResultOurs` | `RWStructuredBuffer` | 着色器资源 |
| `roughness` | `StructuredBuffer` | 着色器资源 |
| `result` | `RWStructuredBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `testPbrtReference` | [1024, 1, 1] | 计算着色器入口点 |
| `testWhiteFurnaceUniform` | [16, 1, 1] | 计算着色器入口点 |
| `testWhiteFurnaceImportanceSampling` | [16, 1, 1] | 计算着色器入口点 |
| `testImportanceSamplingWeights` | [16, 16, 1] | 计算着色器入口点 |
| `testSamplingConsistency` | [16, 1, 1] | 计算着色器入口点 |


## 依赖关系 / import

- `Utils.Math.MathHelpers`
- `Utils.Color.ColorHelpers`
- `Utils.Sampling.SampleGenerator`
- `Rendering.Materials.HairChiang16`


### 关联 C++ 测试

- [`HairChiang16Tests.cpp`](HairChiang16Tests.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
