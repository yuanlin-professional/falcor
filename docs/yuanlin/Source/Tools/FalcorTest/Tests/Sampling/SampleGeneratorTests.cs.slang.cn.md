# SampleGeneratorTests.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Sampling/SampleGeneratorTests.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Sampling

## 功能概述

测试通用采样生成器接口，验证不同采样策略的序列生成。

## 常量缓冲区

- `CB`: 常量缓冲区


## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `result` | `RWStructuredBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `test` | [16, 16, 1] | 计算着色器入口点 |


## 依赖关系 / import

- `Utils.Sampling.SampleGenerator`


### 关联 C++ 测试

- [`SampleGeneratorTests.cpp`](SampleGeneratorTests.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
