# AliasTableTests.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Sampling/AliasTableTests.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Sampling

## 功能概述

测试别名表（Alias Table）离散分布采样算法，验证按指定权重的离散采样分布正确性。

## 常量缓冲区

- `CB`: 常量缓冲区


## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `random` | `StructuredBuffer` | 着色器资源 |
| `sampleResult` | `RWStructuredBuffer` | 着色器资源 |
| `weightResult` | `RWStructuredBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `testAliasTableSample` | [256, 1, 1] | 计算着色器入口点 |
| `testAliasTableWeight` | [256, 1, 1] | 计算着色器入口点 |


## 依赖关系 / import

- `Utils.Sampling.AliasTable`


### 关联 C++ 测试

- [`AliasTableTests.cpp`](AliasTableTests.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
