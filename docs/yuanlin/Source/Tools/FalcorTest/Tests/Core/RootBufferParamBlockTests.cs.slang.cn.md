# RootBufferParamBlockTests.cs.slang 源码文档

> 路径: `Source/Tools/FalcorTest/Tests/Core/RootBufferParamBlockTests.cs.slang`
> 类型: Slang 着色器
> 模块: Tools/FalcorTest/Tests/Core

## 功能概述

测试通过参数块（ParameterBlock）绑定的根缓冲区访问，验证着色器中对根级别结构化缓冲区的读写。

## 资源绑定

| 资源名称 | 类型 | 说明 |
|----------|------|------|
| `result` | `RWStructuredBuffer` | 着色器资源 |
| `globalBufA` | `Buffer` | 着色器资源 |
| `globalTestBuffer` | `RWByteAddressBuffer` | 着色器资源 |
| `bound` | `Buffer` | 着色器资源 |
| `globalTestBuffer` | `ByteAddressBuffer` | 着色器资源 |
| `bound` | `Buffer` | 着色器资源 |


## 函数

| 函数名 | 线程组大小 | 说明 |
|--------|-----------|------|
| `main` | [256, 1, 1] | 计算着色器入口点 |


## 依赖关系 / import

- `Utils.Attributes`
- `ParamBlockDefinition`


### 关联 C++ 测试

- [`RootBufferParamBlockTests.cpp`](RootBufferParamBlockTests.md)


## 实现细节

本着色器文件使用 Slang 语言编写，属于 Falcor 测试套件的 GPU 端代码。通常由对应的 C++ 测试文件调用 `ctx.createProgram()` 加载执行。
