# LowDiscrepancyTests.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Sampling/LowDiscrepancyTests.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Sampling

## 功能概述

测试低差异序列（如 Halton、Sobol）生成器，验证准蒙特卡洛采样点的分布质量。

## 常量缓冲区

- `TestCB`: 常量缓冲区


## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `result` | `RWStructuredBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `testRadicalInverse` | [1, 1, 1] | 计算着色器入口点 |


## 依赖关系 / import

- `Utils.Sampling.LowDiscrepancy.HammersleySequence`


### 关联 C++ 测试

- [`LowDiscrepancyTests.cpp`](LowDiscrepancyTests.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
