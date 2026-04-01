# MathHelpersTests.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Utils/MathHelpersTests.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Utils

## 功能概述

测试数学工具函数，包括 clamp、lerp、smoothstep 等以及浮点数比较。

## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `result` | `RWStructuredBuffer` | 着色器资源 |
| `input` | `StructuredBuffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `testSphericalCoordinates` | [1024, 1, 1] | 计算着色器入口点 |
| `testSphericalCoordinatesRad` | [1024, 1, 1] | 计算着色器入口点 |
| `testErrorFunction` | [25, 1, 1] | 计算着色器入口点 |
| `testInverseErrorFunction` | [25, 1, 1] | 计算着色器入口点 |


## 依赖关系 / import

- `Utils.Math.MathHelpers`
- `Utils.Sampling.TinyUniformSampleGenerator`


### 关联 C++ 测试

- [`MathHelpersTests.cpp`](MathHelpersTests.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
