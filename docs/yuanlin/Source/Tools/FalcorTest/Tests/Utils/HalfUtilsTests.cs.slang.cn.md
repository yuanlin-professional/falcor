# HalfUtilsTests.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Utils/HalfUtilsTests.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Utils

## 功能概述

测试 half 浮点数与 float 之间的转换工具函数。

## 常量缓冲区

- `CB`: 常量缓冲区


## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `inputFloat` | `StructuredBuffer` | 着色器资源 |
| `inputUint` | `StructuredBuffer` | 着色器资源 |
| `resultUint` | `RWStructuredBuffer` | 着色器资源 |
| `resultFloat` | `RWStructuredBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `testFP32ToFP16` | [256, 1, 1] | 计算着色器入口点 |
| `testFP16ToFP32` | [256, 1, 1] | 计算着色器入口点 |
| `testFP32ToFP16ConservativeRounding` | [256, 1, 1] | 计算着色器入口点 |
| `testFP16RoundingMode` | [256, 1, 1] | 计算着色器入口点 |


## 依赖关系 / import

- `Utils.Math.HalfUtils`


### 关联 C++ 测试

- [`HalfUtilsTests.cpp`](HalfUtilsTests.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
