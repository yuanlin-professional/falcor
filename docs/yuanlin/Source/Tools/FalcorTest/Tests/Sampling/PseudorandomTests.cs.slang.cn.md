# PseudorandomTests.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Sampling/PseudorandomTests.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Sampling

## 功能概述

测试伪随机数生成器（如 Xoshiro）的统计特性和种子初始化。

## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `seed` | `ByteAddressBuffer` | 着色器资源 |
| `result` | `RWStructuredBuffer` | 着色器资源 |
| `result64` | `RWStructuredBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `testXoshiro` | [kInstances, 1, 1] | 计算着色器入口点 |
| `testSplitMix` | [kInstances, 1, 1] | 计算着色器入口点 |
| `testLCG` | [kInstances, 1, 1] | 计算着色器入口点 |


## 依赖关系 / import

- `Utils.Sampling.Pseudorandom.Xoshiro`
- `Utils.Sampling.Pseudorandom.SplitMix64`
- `Utils.Sampling.Pseudorandom.LCG`


### 关联 C++ 测试

- [`PseudorandomTests.cpp`](PseudorandomTests.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
